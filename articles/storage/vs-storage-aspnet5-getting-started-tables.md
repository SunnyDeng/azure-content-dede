<properties
	pageTitle="Erste Schritte mit Tabellenspeicher und verbundenen Visual Studio-Diensten (ASP.NET 5) | Microsoft Azure"
	description="Erfahren Sie etwas über die ersten Schritte mit Azure-Tabellenspeicher in einem ASP.NET 5-Projekt in Visual Studio, nachdem Sie mithilfe von verbundenen Visual Studio-Diensten eine Verbindung mit einem Speicherkonto hergestellt haben."
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

# Erste Schritte mit Azure-Tabellenspeicher und verbundenen Visual Studio-Diensten

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-aspnet5-getting-started-tables.md)
> - [What Happened](vs-storage-aspnet5-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-aspnet5-getting-started-blobs.md)
> - [Queues](vs-storage-aspnet5-getting-started-queues.md)
> - [Tables](vs-storage-aspnet5-getting-started-tables.md)

## Übersicht

Dieser Artikel beschreibt, wie die ersten Schritte beim Verwenden von Azure-Tabellenspeicher in Visual Studio aussehen, nachdem Sie über das Visual Studio-Dialogfeld **Verbundene Dienste hinzufügen** in einem ASP.NET 5-Projekt ein Azure-Speicherkonto erstellt oder auf ein solches Konto verwiesen haben.

Der Azure-Tabellenspeicherdienst ermöglicht das Speichern großer Mengen von strukturierten Daten. Der Dienst ist ein NoSQL-Datenspeicher, der authentifizierte Aufrufe von innerhalb und außerhalb der Azure-Cloud akzeptiert. Azure-Tabellen sind hervorragend zur Speicherung strukturierter nicht relationaler Daten geeignet.

Bei einem **Verbundene Dienste hinzufügen**-Vorgang werden die entsprechenden NuGet-Pakete installiert, um auf Azure-Speicher in Ihrem Projekt zuzugreifen, und wird die Verbindungszeichenfolge für das Speicherkonto in Ihren Konfigurationsdateien des Projekts hinzugefügt.

Weitere allgemeine Informationen zur Verwendung von Azure-Tabellenspeicher finden Sie unter [Verwenden des Tabellenspeichers mit .NET](storage-dotnet-how-to-use-tables.md).

Damit Sie beginnen können, müssen Sie zuerst eine Tabelle in Ihrem Speicherkonto erstellen. Wir zeigen Ihnen, wie Sie eine Azure-Tabelle in Code erstellen. Außerdem wird gezeigt, wie Sie grundlegende Tabellen- und Entitätsvorgänge, etwa Hinzufügen, Ändern und Lesen von Tabellenentitäten, ausführen. Die Beispiele sind in C# geschrieben und greifen auf die Azure-Speicherclientbibliothek für .NET zurück.

**HINWEIS**: Einige der APIs, die Aufrufe zum Azure-Speicher in ASP.NET 5 ausführen, arbeiten asynchron. Unter [Asynchrone Programmierung mit Async und Await](http://msdn.microsoft.com/library/hh191443.aspx) finden Sie weitere Informationen. Im nachstehenden Code wird die Programmierung mit Async-Methoden angenommen.

## Zugreifen auf Tabellen im Code

Um auf Tabellen in ASP.NET 5-Projekten zuzugreifen, müssen Sie die folgenden Elemente zu jeder C#-Quelldatei hinzufügen, in der auf Azure-Tabellenspeicher zugegriffen wird.

1. Vergewissern Sie sich, dass die Namespacedeklarationen am Anfang der C#-Datei diese **using**-Anweisungen enthalten.

	    using Microsoft.Framework.Configuration;
	    using Microsoft.WindowsAzure.Storage;
	    using Microsoft.WindowsAzure.Storage.Table;
	    using System.Threading.Tasks;
	    using LogLevel = Microsoft.Framework.Logging.LogLevel;

2. Rufen Sie ein **CloudStorageAccount**-Objekt ab, das die Informationen zu Ihrem Speicherkonto enthält. Verwenden Sie den folgenden Code, um Ihre Speicherverbindungszeichenfolge und Speicherkontoinformationen aus der Azure-Dienstkonfiguration abzurufen.

	    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
	        CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

    **HINWEIS**: Verwenden Sie den gesamten obigen Code vor dem Code in den folgenden Beispielen.

3. Rufen Sie ein **CloudTableClient**-Objekt ab, um auf die Tabellenobjekte in Ihrem Speicherkonto zu verweisen.

	    // Create the table client.
    	CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

4. Rufen Sie ein **CloudTable**-Verweisobjekt ab, um auf eine bestimmte Tabelle und auf bestimmte Entitäten zu verweisen.

    	// Get a reference to a table named "peopleTable"
	    CloudTable table = tableClient.GetTableReference("peopleTable");

## Erstellen einer Tabelle in Code

Fügen Sie zum Erstellen der Azure-Tabelle einfach einen Aufruf von **CreateIfNotExistsAsync()** hinzu.

	// Create the CloudTable if it does not exist
	await table.CreateIfNotExistsAsync();

## Hinzufügen einer Entität zu einer Tabelle

Sie erstellen eine Klasse, mit der die Eigenschaften der Entität definiert werden, um einer Tabelle eine Entität hinzuzufügen. Der folgenden Code definiert eine Entitätsklasse namens **CustomerEntity**, die den Vornamen des Kunden als Zeilenschlüssel und den Nachnamen als Partitionsschlüssel verwendet.

	public class CustomerEntity : TableEntity
	{
	    public CustomerEntity(string lastName, string firstName)
	    {
	        this.PartitionKey = lastName;
	        this.RowKey = firstName;
	    }

	    public CustomerEntity() { }

	    public string Email { get; set; }

	    public string PhoneNumber { get; set; }
	}

Tabellenvorgänge, die Entitäten betreffen, werden mit dem **CloudTable**-Objekt ausgeführt, das Sie zuvor unter "Zugreifen auf Tabellen in Code" erstellt haben. Das **TableOperation**-Objekt stellt den auszuführenden Vorgang dar. Das folgende Codebeispiel zeigt das Erstellen eines **CloudTable**-Objekts und eines **CustomerEntity**-Objekts. Um den Vorgang vorzubereiten, wird eine **TableOperation** zum Einfügen der Kundenentität in die Tabelle erstellt. Schließlich wird der Vorgang durch einen Aufruf von CloudTable.ExecuteAsync ausgeführt.

	// Get a reference to the CloudTable object named 'peopleTable' as described in "Access a table in code"

	// Create a new customer entity.
	CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
	customer1.Email = "Walter@contoso.com";
	customer1.PhoneNumber = "425-555-0101";

	// Create the TableOperation that inserts the customer entity.
	TableOperation insertOperation = TableOperation.Insert(customer1);

	// Execute the insert operation.
	await peopleTable.ExecuteAsync(insertOperation);

## Einfügen eines Entitätsbatchs

Sie können mehrere Entitäten mit einem Schreibvorgang in eine Tabelle einfügen. Das folgende Codebeispiel erstellt zwei Entitätsobjekte ("Jeff Smith" und "Ben Smith"), fügt diese mithilfe der **Insert**-Methode einem **TableBatchOperation**-Objekt hinzu und startet den Vorgang dann durch einen Aufruf von "CloudTable.ExecuteBatchAsync".

	// Get a reference to a CloudTable object named 'peopleTable' as described in "Access a table in code"

	// Create the batch operation.
	TableBatchOperation batchOperation = new TableBatchOperation();

	// Create a customer entity and add it to the table.
	CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
	customer1.Email = "Jeff@contoso.com";
	customer1.PhoneNumber = "425-555-0104";

	// Create another customer entity and add it to the table.
	CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
	customer2.Email = "Ben@contoso.com";
	customer2.PhoneNumber = "425-555-0102";

	// Add both customer entities to the batch insert operation.
	batchOperation.Insert(customer1);
	batchOperation.Insert(customer2);

	// Execute the batch operation.
	await peopleTable.ExecuteBatchAsync(batchOperation);

## Abrufen aller Entitäten in einer Partition
Verwenden Sie ein **TableQuery**-Objekt, um eine Tabelle nach allen Entitäten in einer Partition abzufragen. Im folgenden Codebeispiel wird ein Filter für Entitäten erstellt, wobei "Smith" der Partitionsschlüssel ist. In diesem Beispiel werden die Felder der einzelnen Entitäten in den Abfrageergebnissen an die Konsole ausgegeben.

	// Get a reference to a CloudTable object named 'peopleTable' as described in "Access a table in code"

	// Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    TableContinuationToken token = null;
    do
    {
    	TableQuerySegment<CustomerEntity> resultSegment = await peopleTable.ExecuteQuerySegmentedAsync(query, token);
		token = resultSegment.ContinuationToken;

		foreach (CustomerEntity entity in resultSegment.Results)
    	{
    		Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
    		entity.Email, entity.PhoneNumber);
        }
    } while (token != null);

    return View();


## Abrufen einer einzelnen Entität
Sie können eine Abfrage schreiben, um eine bestimmte Entität abzurufen. Der folgende Code verwendet ein **TableOperation**-Objekt, um einen Kunden namens "Ben Smith" anzugeben. Diese Methode gibt anstelle einer Sammlung nur eine Entität zurück, und bei dem zurückgegebenen Wert in **TableResult.Result** handelt es sich um ein **CustomerEntity**-Objekt. Das Angeben der Partitions- und Zeilenschlüssel in einer Abfrage ist die schnellste Möglichkeit, um eine einzelne Entität aus dem Tabellendienst abzurufen.

	// Get a reference to a CloudTableobject named 'peopleTable' as described in "Access a table in code"

	// Create a retrieve operation that takes a customer entity.
	TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

	// Execute the retrieve operation.
	TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

	// Print the phone number of the result.
	if (retrievedResult.Result != null)
	   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
	else
	   Console.WriteLine("The phone number could not be retrieved.");

## Löschen einer Entität
Sie können eine Entität nach dem Abrufen löschen. Der folgende Code sucht nach einer Kundenentität namens "Ben Smith". Wenn diese gefunden wird, wird sie gelöscht.

	// Get a reference to a CloudTableobject named 'peopleTable' as described in "Access a table in code"

	// Create a retrieve operation that expects a customer entity.
	TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

	// Execute the operation.
	TableResult retrievedResult = peopleTable.Execute(retrieveOperation);

	// Assign the result to a CustomerEntity object.
	CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

	// Create the Delete TableOperation and then execute it.
	if (deleteEntity != null)
	{
	   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

	   // Execute the operation.
	   await peopleTable.ExecuteAsync(deleteOperation);

	   Console.WriteLine("Entity deleted.");
	}

	else
	   Console.WriteLine("Couldn't delete the entity.");

## Nächste Schritte

[AZURE.INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]

<!---HONumber=Oct15_HO3-->