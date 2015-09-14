<properties
	pageTitle="Erste Schritte mit Azure-Tabellenspeicher und verbundenen Visual Studio-Diensten | Microsoft Azure"
	description="Erste Schritte mit Azure-Tabellenspeicher in einem ASP.NET-Projekt in Visual Studio"
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
	ms.date="08/04/2015"
	ms.author="patshea123"/>

# Erste Schritte mit Azure-Tabellenspeicher und verbundenen Visual Studio-Diensten

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-aspnet-getting-started-tables.md)
> - [What Happened](vs-storage-aspnet-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-aspnet-getting-started-blobs.md)
> - [Queues](vs-storage-aspnet-getting-started-queues.md)
> - [Tables](vs-storage-aspnet-getting-started-tables.md)

## Übersicht
Dieser Artikel beschreibt, wie die ersten Schritte beim Verwenden von Azure-Tabellenspeicher in Visual Studio aussehen, nachdem Sie über das Visual Studio-Dialogfeld **Verbundene Dienste hinzufügen** in einem ASP.NET-Projekt ein Azure-Speicherkonto erstellt oder auf ein solches Konto verwiesen haben. In diesem Artikel werden allgemeine Aufgaben in Azure-Tabellen veranschaulicht, einschließlich dem Erstellen und Löschen einer Tabelle sowie dem Arbeiten mit Tabellenentitäten. Die Beispiele sind in C# geschrieben und greifen auf die [Azure-Speicherclientbibliothek für .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx) zurück. Weitere allgemeine Informationen zur Verwendung von Azure-Tabellenspeicher finden Sie unter [Verwenden des Tabellenspeichers mit .NET](storage-dotnet-how-to-use-tables.md).

Azure-Tabellenspeicher ermöglicht das Speichern großer Mengen von strukturierten Daten. Der Dienst ist ein NoSQL-Datenspeicher, der authentifizierte Aufrufe von innerhalb und außerhalb der Azure-Cloud akzeptiert. Azure-Tabellen sind hervorragend zur Speicherung strukturierter nicht relationaler Daten geeignet.


## Zugreifen auf Tabellen im Code

1. Vergewissern Sie sich, dass die Namespace-Deklarationen am Anfang der C#-Datei diese `using`-Anweisungen enthalten.

		 using Microsoft.Azure;
		 using Microsoft.WindowsAzure.Storage;
		 using Microsoft.WindowsAzure.Storage.Auth;
		 using Microsoft.WindowsAzure.Storage.Table;

2. Rufen Sie ein `CloudStorageAccount`-Objekt ab, das die Informationen zu Ihrem Speicherkonto enthält. Verwenden Sie den folgenden Code, um Ihre Speicherverbindungszeichenfolge und Speicherkontoinformationen aus der Azure-Dienstkonfiguration abzurufen.

		 CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		   CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

    **HINWEIS**: Verwenden Sie den gesamten obigen Code vor dem Code in den folgenden Beispielen.

3. Rufen Sie ein `CloudTableClient`-Objekt ab, um auf die Tabellenobjekte in Ihrem Speicherkonto zu verweisen.

	    // Create the table client.
    	CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

4. Rufen Sie ein `CloudTable`-Verweisobjekt ab, um auf eine bestimmte Tabelle und auf bestimmte Entitäten zu verweisen.

    	// Get a reference to a table named "peopleTable"
	    CloudTable table = tableClient.GetTableReference("peopleTable");

## Erstellen einer Tabelle in Code

Fügen Sie zum Erstellen einer Azure-Tabelle einfach einen Aufruf von `CreateIfNotExistsAsync()` zum vorherigen Code hinzu.

	// Create the CloudTable if it does not exist
	await table.CreateIfNotExistsAsync();


## Einfügen eines Entitätsbatchs

Sie können mehrere Entitäten mit einem Schreibvorgang in eine Tabelle einfügen. Das folgende Codebeispiel erstellt zwei Entitätsobjekte ("Jeff Smith" und "Ben Smith"), fügt diese mithilfe der Methode "Insert" einem `TableBatchOperation`-Objekt hinzu und startet den Vorgang dann durch einen Aufruf von `CloudTable.ExecuteBatchAsync`.

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
Verwenden Sie ein `TableQuery`-Objekt, um eine Tabelle nach allen Entitäten in einer Partition abzufragen. Im folgenden Codebeispiel wird ein Filter für Entitäten erstellt, wobei "Smith" der Partitionsschlüssel ist. In diesem Beispiel werden die Felder der einzelnen Entitäten in den Abfrageergebnissen an die Konsole ausgegeben.

	// Get a reference to a CloudTable object named 'peopleTable' as described in "Access a table in code"

	// Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    TableContinuationToken token = null;
    do
    {
    	TableQuerySegment<CustomerEntity>
        resultSegment = await peopleTable.ExecuteQuerySegmentedAsync(query, token);
        token = resultSegment.ContinuationToken;

        foreach (CustomerEntity entity in resultSegment.Results)
        {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
        }
        } while (token != null);

        return View();


## Abrufen einer einzelnen Entität
Sie können eine Abfrage schreiben, um eine bestimmte Entität abzurufen. Der folgende Code verwendet ein `TableOperation`-Objekt, um einen Kunden namens "Ben Smith" anzugeben. Bei dieser Methode wird nur eine Entität anstelle einer Sammlung zurückgegeben, und der zurückgegebene Wert in `TableResult.Result` ist ein `CustomerEntity`-Objekt. Das Angeben der Partitions- und Zeilenschlüssel in einer Abfrage ist die schnellste Möglichkeit, um eine einzelne Entität aus dem Tabellendienst abzurufen.

        // Get a reference to a CloudTable object named 'peopleTable' as described in "Access a table in code"

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

	// Get a reference to a CloudTable object named 'peopleTable' as described in "Access a table in code"

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

[AZURE.INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]

<!---HONumber=September15_HO1-->