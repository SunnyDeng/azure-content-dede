<properties 
	pageTitle="Erste Schritte mit Azure Storage" 
	description="Erste Schritte mit Azure-Tabellenspeicher in einem Clouddienstprojekt in Visual Studio" 
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
	ms.date="07/17/2015" 
	ms.author="patshea123"/>

# Erste Schritte mit Azure Storage (Cloud Service-Projekte)

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-cloud-services-getting-started-tables.md)
> - [What Happened](vs-storage-cloud-services-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-cloud-services-getting-started-blobs.md)
> - [Queues](vs-storage-cloud-services-getting-started-queues.md)
> - [Tables](vs-storage-cloud-services-getting-started-tables.md)

##Übersicht

Der Azure-Tabellenspeicherdienst ermöglicht das Speichern großer Mengen von strukturierten Daten. Der Dienst ist ein NoSQL-Datenspeicher, der authentifizierte Aufrufe von innerhalb und außerhalb der Azure-Cloud akzeptiert. Azure-Tabellen sind hervorragend zur Speicherung strukturierter nicht relationaler Daten geeignet.

Dieser Artikel beschreibt, wie die ersten Schritte des Verwendens von Azure-Tabellenspeicher in Visual Studio aussehen, nachdem Sie über das Visual Studio-Dialogfeld **Verbundene Dienste hinzufügen** in einem CI-Projekt ein Azure-Speicherkonto erstellt oder auf ein solches Konto verwiesen haben.

Bei einem **Verbundene Dienste hinzufügen**-Vorgang werden die entsprechenden NuGet-Pakete installiert, um auf Azure-Speicher in Ihrem Projekt zuzugreifen, und wird die Verbindungszeichenfolge für das Speicherkonto in Ihren Konfigurationsdateien des Projekts hinzugefügt.

Weitere allgemeine Informationen zur Verwendung von Azure-Tabellenspeicher finden Sie unter [Verwenden des Tabellenspeichers mit .NET](storage-dotnet-how-to-use-tables.md).

Damit Sie beginnen können, müssen Sie zuerst eine Tabelle in Ihrem Speicherkonto erstellen. Es wird gezeigt, wie Sie aus Visual Studio **Server-Explorer** eine Azure-Tabelle erstellen. Möglicherweise bevorzugen Sie es, eine Tabelle in Code zu erstellen. Auch dies wird gezeigt.

Außerdem wird gezeigt, wie Sie grundlegende Tabellen- und Entitätsvorgänge, etwa Hinzufügen, Ändern und Lesen von Tabellenentitäten, ausführen. Die Beispiele sind in C# geschrieben und greifen auf die Azure-Speicherclientbibliothek für .NET zurück.

**HINWEIS:** Einige der APIs, die Aufrufe zum Azure-Speicher ausführen, sind asynchron. Unter [Asynchrone Programmierung mit Async und Await](http://msdn.microsoft.com/library/hh191443.aspx) finden Sie weitere Informationen. Im folgenden Code wird die Programmierung mit Async-Methoden angenommen.

##Erstellen von Azure-Speichertabellen in Visual Studio Server-Explorer

[AZURE.INCLUDE [vs-create-table-in-server-explorer](../../includes/vs-create-table-in-server-explorer.md)]

##**Zugreifen auf Tabellen in Code** 

Um auf Tabellen in Clouddienst-Projekten zuzugreifen, müssen Sie die folgenden Elemente zu jeder C#-Quelldatei hinzufügen, in der auf Azure-Tabellenspeicher zugegriffen wird.

1. Vergewissern Sie sich, dass die Namespace-Deklarationen am Anfang der C#-Datei diese `using`-Anweisungen enthalten.

		using Microsoft.Framework.Configuration;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Table;
		using System.Threading.Tasks;
		using LogLevel = Microsoft.Framework.Logging.LogLevel;

2. Rufen Sie ein **CloudStorageAccount**-Objekt ab, das die Informationen zu Ihrem Speicherkonto enthält. Verwenden Sie den folgenden Code, um Ihre Speicherverbindungszeichenfolge und Speicherkontoinformationen aus der Azure-Dienstkonfiguration abzurufen.

		 CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		   CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));

    **HINWEIS:** Verwenden Sie den gesamten obigen Code vor dem Code in den folgenden Beispielen.


3. Rufen Sie ein **CloudTableClient**-Objekt ab, um auf die Tabellenobjekte in Ihrem Speicherkonto zu verweisen.

	    // Create the table client.
    	CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

4. Rufen Sie ein **CloudTable**-Verweisobjekt ab, auf eine bestimmte Tabelle und auf bestimmte Entitäten zu verweisen.
	
    	// Get a reference to a table named "peopleTable"
	    CloudTable table = tableClient.GetTableReference("peopleTable");

###Erstellen einer Tabelle in Code

Um die Azure-Tabelle in Code statt mit Visual Studio **Server-Explorer** zu erstellen, fügen Sie einfach einen Aufruf von `CreateIfNotExistsAsync()` hinzu.

	// Create the CloudTable if it does not exist
	await table.CreateIfNotExistsAsync();

##Hinzufügen einer Entität zu einer Tabelle

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

Tabellenvorgänge, die Entitäten betreffen, werden mit dem **CloudTable**-Objekt ausgeführt, das Sie zuvor unter „Zugreifen auf Tabellen in Code“ erstellt haben. Das **TableOperation**-Objekt stellt den auszuführenden Vorgang dar. Das folgende Codebeispiel zeigt das Erstellen eines **CloudTable**-Objekts und eines **CustomerEntity**-Objekts. Um den Vorgang vorzubereiten, wird eine **TableOperation** zum Einfügen der Kundenentität in die Tabelle erstellt. Schließlich wird der Vorgang durch einen Aufruf von CloudTable.ExecuteAsync ausgeführt.

	// Get a reference to the **CloudTable** object named 'peopleTable' as described in "Access a table in code"

	// Create a new customer entity.
	CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
	customer1.Email = "Walter@contoso.com";
	customer1.PhoneNumber = "425-555-0101";
	
	// Create the TableOperation that inserts the customer entity.
	TableOperation insertOperation = TableOperation.Insert(customer1);
	
	// Execute the insert operation.
	await peopleTable.ExecuteAsync(insertOperation);

##Einfügen eines Entitätsbatchs

Sie können mehrere Entitäten mit einem Schreibvorgang in eine Tabelle einfügen. Das folgende Codebeispiel erstellt zwei Entitätsobjekte („Jeff Smith“ und „Ben Smith“), fügt diese mithilfe der Insert-Methode einem **TableBatchOperation**-Objekt hinzu und startet den Vorgang dann durch einen Aufruf von CloudTable.ExecuteBatchAsync.

	// Get a reference to a **CloudTable** object named 'peopleTable' as described in "Access a table in code"
	
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

##Abrufen aller Entitäten in einer Partition
Verwenden Sie ein **TableQuery**-Objekt, um eine Tabelle nach allen Entitäten in einer Partition abzufragen. Im folgenden Codebeispiel wird ein Filter für Entitäten erstellt, wobei "Smith" der Partitionsschlüssel ist. In diesem Beispiel werden die Felder der einzelnen Entitäten in den Abfrageergebnissen an die Konsole ausgegeben.

	// Get a reference to a **CloudTable** object named 'peopleTable' as described in "Access a table in code"

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


##Abrufen einer einzelnen Entität
Sie können eine Abfrage schreiben, um eine bestimmte Entität abzurufen. Der folgende Code verwendet ein **TableOperation**-Objekt, um einen Kunden namens "Ben Smith" anzugeben. Diese Methode gibt nur eine Entität anstelle einer Sammlung zurück, und bei dem zurückgegebenen Wert in TableResult.Result handelt es sich um ein **CustomerEntity**-Objekt. Das Angeben der Partitions- und Zeilenschlüssel in einer Abfrage ist die schnellste Möglichkeit, um eine einzelne Entität aus dem Tabellendienst abzurufen.

	// Get a reference to a **CloudTable** object named 'peopleTable' as described in "Access a table in code"
	
	// Create a retrieve operation that takes a customer entity.
	TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
	
	// Execute the retrieve operation.
	TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);
	
	// Print the phone number of the result.
	if (retrievedResult.Result != null)
	   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
	else
	   Console.WriteLine("The phone number could not be retrieved.");

##Löschen einer Entität
Sie können eine Entität nach dem Abrufen löschen. Der folgende Code sucht nach einer Kundenentität namens "Ben Smith". Wenn diese gefunden wird, wird sie gelöscht.

	// Get a reference to a **CloudTable** object named 'peopleTable' as described in "Access a table in code"
	
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

##Nächste Schritte

[AZURE.INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]



[Weitere Informationen zu Azure Storage](http://azure.microsoft.com/documentation/services/storage/) finden Sie unter [Durchsuchen von Speicherressourcen mit Server-Explorer](http://msdn.microsoft.com/library/azure/ff683677.aspx) und [ASP.NET 5](http://www.asp.net/vnext).
 

<!---HONumber=July15_HO4-->