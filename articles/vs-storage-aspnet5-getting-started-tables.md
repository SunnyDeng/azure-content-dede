<properties 
	pageTitle="Erste Schritte mit Azure Storage" 
	description="" 
	services="storage" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Erste Schritte](/documentation/articles/vs-storage-aspnet5-getting-started-tables/)
> - [Was ist passiert?](/documentation/articles/vs-storage-aspnet5-what-happened/)

## Erste Schritte mit Azure Storage (ASP.NET vNext-Projekte)

> [AZURE.SELECTOR]
> - [Blobs](/documentation/articles/vs-storage-aspnet5-getting-started-blobs/)
> - [Warteschlangen](/documentation/articles/vs-storage-aspnet5-getting-started-queues/)
> - [Tabellen](/documentation/articles/vs-storage-aspnet5-getting-started-tables/)

Der Azure-Tabellenspeicherdienst ermöglicht das Speichern großer Mengen von strukturierten Daten. Der Dienst ist ein NoSQL-Datenspeicher, der authentifizierte Aufrufe von innerhalb und außerhalb der Azure-Cloud akzeptiert. Azure-Tabellen sind hervorragend zur Speicherung strukturierter nicht relationaler Daten geeignet.  Weitere Informationen finden Sie unter[Verwenden von Tabellenspeicher aus .NET](http://azure.microsoft.com/de-de/documentation/articles/storage-dotnet-how-to-use-tables/#create-table "How to use Table Storage from .NET").

Für den programmgesteuerten Zugriff auf Tabellen in ASP.NET 5-Projekten müssen Sie die folgenden Elemente hinzuzufügen, wenn sie nicht bereits vorhanden sind.

1. Fügen Sie die folgende Codenamespace-Deklaration am Anfang aller C#-Dateien hinzu, mit denen Sie programmgesteuert auf Azure Storage zugreifen möchten.

		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Table;
		using Microsoft.Framework.ConfigurationModel;
		using System.Threading.Tasks;

2. Verwenden Sie den folgenden Code, um die Konfigurationseinstellung abzurufen.

		Configuration = new Configuration()
                .AddJsonFile("config.json")
                .AddEnvironmentVariables();

##### Abrufen der Verbindungszeichenfolge des Speichers
Bevor Sie eine Tabelle verwenden können, müssen Sie die Verbindungszeichenfolge für das Speicherkonto abrufen, in dem die Tabellen gespeichert werden. Sie können den Typ **CloudStorageAccount** verwenden, um Ihre Speicherkontoinformationen darzustellen. Wenn Sie ein ASP.NET vNext-Projekt verwenden, können Sie die get-Methode des Konfigurationsobjekts verwenden, um Ihre Verbindungszeichenfolge für den Speicher und die Speicherkontoinformationen aus der Azure-Dienstkonfiguration abzurufen. Der folgende Code zeigt dies.

**HINWEIS:** Die APIs, welche die Aufrufe für den Azure-Speicher in ASP.NET 5 ausführen, sind asynchron. Weitere Informationen finden Sie unter [Asynchrone Programmierung mit Async und Await](http://msdn.microsoft.com/library/hh191443.aspx). Im folgenden Code wird davon ausgegangen, dass Async-Programmierungsmethoden verwendet werden.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.Get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

##### Erstellen einer Tabelle
Mit einem **CloudTableClient**-Objekt können Sie Referenzobjekte für Tabellen und Entitäten abrufen. Der folgende Code erstellt ein **CloudTableClient**-Objekt und verwendet das Objekt für die Erstellung einer neuen Tabelle. Der Code versucht, auf eine Tabelle namens "people" zu verweisen. Wird keine Tabelle mit diesem Namen gefunden, wird die Tabelle erstellt.

**HINWEIS:** Der gesamte Code in diesem Leitfaden setzt voraus, dass es sich bei der erstellten Anwendung um ein Azure Cloud Service-Projekt handelt und dass eine in der Dienstkonfiguration der Azure-Anwendung gespeicherte Verbindungszeichenfolge für den Speicher verwendet wird. Verwenden Sie zudem all diesen Code vor dem Code in den folgenden Abschnitten.

	// Create the table client.
	CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
	
	// Create the table if it doesn't exist.
	CloudTable table = tableClient.GetTableReference("people");
	await table.CreateIfNotExistsAsync();

##### Hinzufügen einer Entität zu einer Tabelle
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

Tabellenvorgänge, die Entitäten umfassen, werden mit dem **CloudTable**-Objekt ausgeführt, das Sie zuvor unter "Erstellen einer Tabelle" erstellt haben. Das **TableOperation**-Objekt stellt den auszuführenden Vorgang dar. Das folgende Codebeispiel zeigt das Erstellen eines **CloudTable**-Objekts und eines **CustomerEntity**-Objekts. Als Vorbereitung für den Vorgang wird ein **TableOperation**-Objekt erstellt, um die Kundenentität in die Tabelle einzufügen. Schließlich wird der Vorgang durch einen Aufruf von CloudTable.ExecuteAsync ausgeführt.

	// Create a new customer entity.
	CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
	customer1.Email = "Walter@contoso.com";
	customer1.PhoneNumber = "425-555-0101";
	
	// Create the TableOperation that inserts the customer entity.
	TableOperation insertOperation = TableOperation.Insert(customer1);
	
	// Execute the insert operation.
	await table.ExecuteAsync(insertOperation);

##### Einfügen eines Entitätsbatchs
Sie können mehrere Entitäten mit einem Schreibvorgang in eine Tabelle einfügen. Das folgende Codebeispiel erstellt zwei Entitätsobjekte ("Jeff Smith" und "Ben Smith"), fügt diese mithilfe der Insert-Methode einem **TableBatchOperation**-Objekt hinzu und startet den Vorgang dann durch einen Aufruf von calling CloudTable.ExecuteBatchAsync.

	// Create the CloudTable object that represents the "people" table.
	CloudTable table = tableClient.GetTableReference("people");
	
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
	await table.ExecuteBatchAsync(batchOperation);

##### Abrufen aller Entitäten in einer Partition
Verwenden Sie ein **TableQuery**-Objekt, um eine Tabelle nach allen Entitäten in einer Partition abzufragen. Im folgenden Codebeispiel wird ein Filter für Entitäten erstellt, wobei 'Smith' der Partitionsschlüssel ist. In diesem Beispiel werden die Felder der einzelnen Entitäten in den Abfrageergebnissen an die Konsole ausgegeben.

	// Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    TableContinuationToken token = null;
    do
    {
    	TableQuerySegment<CustomerEntity> resultSegment = await table.ExecuteQuerySegmentedAsync(query, token);
		token = resultSegment.ContinuationToken;

		foreach (CustomerEntity entity in resultSegment.Results)
    	{
    		Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
    		entity.Email, entity.PhoneNumber);
        }
    } while (token != null);

    return View();


##### Abrufen einer einzelnen Entität
Sie können eine Abfrage schreiben, um eine bestimmte Entität abzurufen. Der folgende Code verwendet ein **TableOperation**-Objekt, um einen Kunden namens 'Ben Smith' anzugeben. Diese Methode gibt nur eine Entität anstelle einer Sammlung zurück, und bei dem zurückgegebenen Wert in TableResult.Result handelt es sich um ein **CustomerEntity**-Objekt. Die Angabe beider Schlüssel, Partition und Zeile, in einer Abfrage ist die schnellste Möglichkeit, um eine einzelne Entität aus dem **Tabellendienst** abzurufen.

	// Create the CloudTable object that represents the "people" table.
	CloudTable table = tableClient.GetTableReference("people");
	
	// Create a retrieve operation that takes a customer entity.
	TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
	
	// Execute the retrieve operation.
	TableResult retrievedResult = await table.ExecuteAsync(retrieveOperation);
	
	// Print the phone number of the result.
	if (retrievedResult.Result != null)
	   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
	else
	   Console.WriteLine("The phone number could not be retrieved.");

##### Löschen einer Entität
Sie können eine Entität nach dem Abrufen löschen. Der folgende Code sucht nach einer Kundenentität namens "Ben Smith". Wenn diese gefunden wird, wird sie gelöscht.

	// Create the CloudTable that represents the "people" table.
	CloudTable table = tableClient.GetTableReference("people");
	
	// Create a retrieve operation that expects a customer entity.
	TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
	
	// Execute the operation.
	TableResult retrievedResult = table.Execute(retrieveOperation);
	
	// Assign the result to a CustomerEntity object.
	CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;
	
	// Create the Delete TableOperation and then execute it.
	if (deleteEntity != null)
	{
	   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
	
	   // Execute the operation.
	   await table.ExecuteAsync(deleteOperation);
	
	   Console.WriteLine("Entity deleted.");
	}
	
	else
	   Console.WriteLine("Couldn't delete the entity.");

[Weitere Informationen zu Azure Storage](http://azure.microsoft.com/documentation/services/storage/)
Weitere Informationen finden Sie unter [Durchsuchen von Speicherressourcen mit Server-Explorer](http://msdn.microsoft.com/de-de/library/azure/ff683677.aspx) und [ASP.NET 5](http://www.asp.net/vnext).

<!--HONumber=42-->
