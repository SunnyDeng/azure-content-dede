<properties 
	pageTitle="Verwenden des Tabellenspeichers mit .NET | Microsoft Azure" 
	description="Erfahren Sie, wie Sie den Microsoft Azure-Tabellenspeicher zum Erstellen und Löschen von Tabellen sowie zum Einfügen und Abfragen von Entitäten in Tabellen verwenden." 
	services="storage" 
	documentationCenter=".net" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/11/2015" 
	ms.author="tamram"/>


# Verwenden des Tabellenspeichers mit .NET

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

## Übersicht

In diesem Leitfaden wird die Durchführung häufiger Szenarien mit dem 
Azure-Tabellenspeicherdienst demonstriert. Die Beispiele sind in C# geschrieben und greifen auf die Azure Storage Client Library für .NET zurück. Die behandelten Szenarien umfassen das **Erstellen und Löschen einer Tabelle** sowie das **Arbeiten mit Tabellenentitäten**.

> [AZURE.NOTE] Diese Anleitung gilt für die Azure .NET Storage Client Library 2.x und höher. Die empfohlene Version für die Storage-Clientbibliothek ist 4.x. Diese Version ist entweder über [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) oder als Teil des [Azure-SDK für .NET](/downloads/) erhältlich. Unter [Programmgesteuerter Zugriff auf Tabellenspeicher](#programmatically-access-table-storage) weiter unten finden Sie weitere Informationen zum Download der Storage-Clientbibliothek.

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-configure-connection-string-include](../../includes/storage-configure-connection-string-include.md)]

## Programmgesteuerter Zugriff auf Tabellenspeicher

### Abrufen der Assembly
Sie können die Assembly  `Microsoft.WindowsAzure.Storage.dll` mit NuGet abrufen. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf Ihr Projekt, und wählen Sie **NuGet-Pakete verwalten** aus.  Suchen Sie online nach "WindowsAzure.Storage", und klicken Sie dann auf **Installieren**, um das Azure Storage-Paket und die zugehörigen Abhängigkeiten zu installieren.

"Microsoft.WindowsAzure.Storage.dll" ist ebenfalls im Azure-SDK für .NET enthalten, das Sie im <a href="http://azure.microsoft.com/develop/net/#">.NET Developer Center</a> herunterladen können. Die Assembly wird im Verzeichnis `%Program Files%\Microsoft SDKs\Azure.NET SDK<sdk-version>\ref` installiert.

### Namespace-Deklarationen
Fügen Sie die folgende Codenamespace-Deklaration am Anfang aller C#-Dateien hinzu, mit denen Sie programmgesteuert auf Azure Storage zugreifen möchten:

    using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;

Stellen Sie sicher, dass auf die  `Microsoft.WindowsAzure.Storage.dll`-Assembly verwiesen wird.

### Abrufen der Verbindungszeichenfolge
Sie können Ihre Speicherkontoinformationen mit dem Typ **CloudStorageAccount** darstellen. Falls Sie eine 
Azure-Projektvorlage verwenden und/oder über einen Verweis auf
den Microsoft.WindowsAzure.CloudConfigurationManager-Namespace verfügen, können Sie den Typ **ConfigurationManager** verwenden, um Ihre Verbindungszeichenfolge für den Speicher und die Speicherkontoinformationen aus der Azure-Dienstkonfiguration abzurufen:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Wenn Sie eine Anwendung ohne einen Verweis auf Microsoft.WindowsAzure.CloudConfigurationManager erstellen und die Verbindungszeichenfolge sich, wie oben gezeigt, in `web.config` oder `app.config` befindet, können Sie die Verbindungszeichenfolge mit **ConfigurationManager** abrufen.  Sie müssen einen Verweis auf "System.Configuration.dll" zu Ihrem Projekt und eine andere Namespace-Deklaration dafür hinzufügen:

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"]);

### ODataLib-Abhängigkeiten
ODataLib-Abhängigkeiten in der Speicherclientbibliothek für .NET werden durch die ODataLib-Pakete (Version 5.0.2), die über NuGet verfügbar sind, und nicht durch WCF Data Services aufgelöst.  Die ODataLib-Bibliotheken können direkt heruntergeladen werden, oder es wird über Ihr Codeprojekt durch NuGet darauf verwiesen.  Die spezifischen ODataLib-Pakete sind [OData], [Edm] und [Spatial].

## Erstellen einer Tabelle

Mit einem **CloudTableClient**-Objekt können Sie Referenzobjekte für Tabellen und Entitäten abrufen. Der folgende Code erstellt ein **CloudTableClient**-Objekt und verwendet es zum Erstellen einer neuen Tabelle. Bei allem Code in diesem Leitfaden wird davon ausgegangen, dass es sich bei der erstellten Anwendung um ein Azure-Clouddienstprojekt handelt und eine in der Dienstkonfiguration der Azure-Anwendung gespeicherte Speicherverbindungszeichenfolge verwendet wird.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the table if it doesn't exist.
    CloudTable table = tableClient.GetTableReference("people");
    table.CreateIfNotExists();

## Hinzufügen einer Entität zu einer Tabelle

Entitäten werden C#-Objekten mithilfe einer benutzerdefinierten Klasse zugeordnet, die aus
**TableEntity** abgeleitet wird. Erstellen Sie eine Klasse, mit der die Eigenschaften der Entität definiert werden, um eine Entität zu einer Tabelle hinzuzufügen. Mit dem folgenden Code wird eine Entitätsklasse definiert, die den Vornamen des Kunden als Zeilenschlüssel und den Nachnamen als Partitionsschlüssel verwendet. In Kombination miteinander wird mit dem Partitions- und Zeilenschlüssel eine Entität in der Tabelle eindeutig identifiziert. Entitäten mit demselben Partitionsschlüssel können schneller abgerufen werden als Entitäten mit unterschiedlichen Partitionsschlüsseln, die Verwendung verschiedener Partitionsschlüssel ermöglicht jedoch eine größere parallele Vorgangsskalierbarkeit.  Bei jeder Eigenschaft, die im Tabellendienst gespeichert werden soll, muss es sich um eine öffentliche Eigenschaft eines unterstützten Typs handeln, die sowohl `get` als auch `set` verfügbar macht.
Darüber hinaus  *must* der Entitätstyp einen parameterlosen Konstruktor verfügbar machen.

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

Tabellenvorgänge, die Entitäten umfassen, werden mit dem **CloudTable**-Objekt durchgeführt, das Sie unter "Vorgehensweise: Erstellen einer Tabelle" erstellt haben.  Der durchzuführende Vorgang wird durch ein **TableOperation**-Objekt dargestellt.  Im folgenden Codebeispiel wird die Erstellung des **CloudTable**-Objekts und danach eines **CustomerEntity**-Objekts gezeigt.  Um den Vorgang vorzubereiten, wird eine **TableOperation** zum Einfügen der Kundenentität in die Tabelle erstellt.  Schließlich wird der Vorgang durch einen Aufruf von **CloudTable.Execute** ausgeführt.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
	
	// Create the CloudTable object that represents the "people" table.
	CloudTable table = tableClient.GetTableReference("people");

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    table.Execute(insertOperation);

## Einfügen eines Entitätsbatchs

Sie können einen Entitätsbatch in einem Schreibvorgang in eine Tabelle einfügen. Beachten Sie im Zusammenhang mit Batchvorgängen Folgendes:

1.  Sie können Aktualisierungs-, Lösch- und Einfügevorgänge in einem einzigen Batchvorgang ausführen.
2.  Ein einzelner Batchvorgang kann bis zu 100 Entitäten umfassen.
3.  Alle Entitäten in einem Batchvorgang müssen über denselben
    Partitionsschlüssel verfügen.
4.  Eine Abfrage kann als Batchvorgang durchgeführt werden, dabei muss es sich jedoch um den einzigen Vorgang im Batch handeln.

<!-- -->
Im folgenden Codebeispiel werden zwei Entitätsobjekte erstellt und mit der **Insert**-Methode jeweils einer **TableBatchOperation** hinzugefügt. Danach wird **CloudTable.Execute** aufgerufen, um den Vorgang auszuführen.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    
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
	table.ExecuteBatch(batchOperation);

## Abrufen aller Entitäten in einer Partition

Verwenden Sie ein **TableQuery**-Objekt, um eine Tabelle für alle Entitäten in einer Partition abzurufen.
Im folgenden Codebeispiel wird ein Filter für Entitäten erstellt, wobei  'Smith' der Partitionsschlüssel ist. In diesem Beispiel werden die Felder der einzelnen Entitäten in den Abfrageergebnissen an die Konsole ausgegeben.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    foreach (CustomerEntity entity in table.ExecuteQuery(query))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

## Abrufen eines Entitätsbereichs in einer Partition

Wenn Sie nicht alle Entitäten in einer Partition abrufen möchten, können Sie einen Bereich angeben, indem Sie den Partitionsschlüsselfilter mit einem Zeilenschlüsselfilter kombinieren. Im folgenden Codebeispiel werden zwei Filter eingesetzt, um alle Entitäten in der Partition  'Smith' abzurufen, deren Zeilenschlüssel (Vorname) mit einem Buchstaben vor dem Buchstaben E im Alphabet beginnen. Danach werden die Abfrageergebnisse gedruckt.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

	// Create the table query.
    TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "E")));

    // Loop through the results, displaying information about the entity.
    foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

## Abrufen einer einzelnen Entität

Sie können eine Abfrage schreiben, um eine bestimmte Entität abzurufen. Der folgende Code verwendet eine **TableOperation**, um den Kunden  'Ben Smith' anzugeben.
Bei dieser Methode wird nur eine Entität anstelle einer Sammlung zurückgegeben, und bei dem zurückgegebenen Wert in **TableResult.Result** handelt es sich um eine **CustomerEntity**.
Die Angabe beider Schlüssel, Partition und Zeile, in einer Abfrage ist die schnellste Möglichkeit, um eine einzelne Entität aus dem Tabellendienst abzurufen.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the retrieve operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Print the phone number of the result.
	if (retrievedResult.Result != null)
	   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
	else
	   Console.WriteLine("The phone number could not be retrieved.");

## Ersetzen einer Entität

Um eine Entität zu aktualisieren, rufen Sie sie aus dem Tabellendienst ab, ändern Sie das Entitätsobjekt, und speichern Sie die Änderungen dann im Tabellendienst. Mit dem folgenden Code wird die Telefonnummer eines vorhandenen Kunden geändert. Anstelle von **Insert** wird in diesem Code 
**Replace** aufgerufen. Dadurch wird die Entität auf dem Server vollständig ersetzt, sofern die Entität auf dem Server sich seit dem letzten Abruf nicht geändert hat. In diesem Fall wäre der Vorgang nicht erfolgreich.  Auf diese Weise soll verhindert werden, dass eine Änderung, die zwischen dem Abruf und der Aktualisierung durch eine andere Komponente der Anwendung vorgenommen wurde, unabsichtlich überschrieben wird.  In diesem Fall sollten Sie die Entität erneut abrufen, Ihre Änderungen vornehmen (falls diese noch gültig sind) und anschließend einen neuen **Replace**-Vorgang ausführen.  Im nächsten Abschnitt erfahren Sie, wie Sie dieses Verhalten überschreiben.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
	{
	   // Change the phone number.
	   updateEntity.PhoneNumber = "425-555-0105";

	   // Create the InsertOrReplace TableOperation
	   TableOperation updateOperation = TableOperation.Replace(updateEntity);

	   // Execute the operation.
	   table.Execute(updateOperation);

	   Console.WriteLine("Entity updated.");
	}

	else
	   Console.WriteLine("Entity could not be retrieved.");

## Einfügen oder Ersetzen einer Entität

**Replace**-Vorgänge sind nicht erfolgreich, wenn die Entität seit dem letzten Abruf vom Server geändert wurde.  Darüber hinaus müssen Sie zuerst die Entität vom Server abrufen, damit der **Replace**-Vorgang erfolgreich ist.
Manchmal ist jedoch nicht bekannt, ob die Entität auf dem Server vorhanden ist, und die darin aktuell gespeicherten Werte sind nicht relevant. In diesem Fall sollten diese durch die Aktualisierung vollständig überschrieben werden.  Dazu würden Sie einen **InsertOrReplace**-Vorgang verwenden.  Bei diesem Vorgang wird die Entität eingefügt, falls sie nicht vorhanden ist, oder ersetzt, falls sie vorhanden ist, unabhängig davon, wann die letzte Aktualisierung stattgefunden hat.  Im folgenden Codebeispiel wird die Kundenentität für Ben Smith ebenfalls abgerufen, anschließend jedoch mithilfe von **InsertOrReplace** auf dem Server gespeichert.  Alle Änderungen, die zwischen dem Abruf- und Aktualisierungsvorgang an der Entität vorgenommen wurden, werden überschrieben.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
	{
	   // Change the phone number.
	   updateEntity.PhoneNumber = "425-555-1234";

	   // Create the InsertOrReplace TableOperation
	   TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(updateEntity);

	   // Execute the operation.
	   table.Execute(insertOrReplaceOperation);

	   Console.WriteLine("Entity was updated.");
	}

	else
	   Console.WriteLine("Entity could not be retrieved.");

## Abfragen einer Teilmenge von Entitätseigenschaften

Mit einer Tabellenabfrage können nicht nur alle, sondern auch nur einige Eigenschaften aus einer Entität abgerufen werden. Bei dieser Methode, der sogenannten Projektion, wird die Bandbreite reduziert und die Abfrageleistung gesteigert, vor allem bei großen Entitäten. Mit der Abfrage im folgenden Code werden nur die E-Mail-Adressen von Entitäten in der Tabelle zurückgegeben. Dies erfolgt durch die Verwendung einer Abfrage von **DynamicTableEntity** und eines **EntityResolver**. Weitere Informationen zur Projektion finden Sie in diesem [Blogbeitrag][]. Beachten Sie, dass die Projektion nicht auf dem lokalen Speicheremulator unterstützt wird und dieser Code deshalb nur bei der Verwendung eines Kontos für den Tabellendienst ausgeführt wird.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Define the query, and only select the Email property
    TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });

    // Define an entity resolver to work with the entity after retrieval.
    EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;

    foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
    {
        Console.WriteLine(projectedEmail);
    }

## Löschen einer Entität

Sie können eine Entität problemlos nach dem Abrufen löschen. Verwenden Sie dazu dasselbe Muster wie für das Aktualisieren einer Entität.  Durch den nachstehenden Code wird eine Kundenentität aufgerufen und gelöscht.

    // Speicherkonto aus Verbindungszeichenfolge abrufen.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that expects a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity.
    CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

    // Create the Delete TableOperation.
	if (deleteEntity != null)
	{
	   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

	   // Execute the operation.
	   table.Execute(deleteOperation);

	   Console.WriteLine("Entity deleted.");
	}

	else
	   Console.WriteLine("Could not retrieve the entity.");

## Löschen einer Tabelle

Schließlich wird mit dem folgenden Codebeispiel eine Tabelle aus einem Speicherkonto gelöscht. Eine gelöschte Tabelle kann für eine bestimmte Zeitdauer nach dem Löschvorgang nicht neu erstellt werden.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Delete the table it if exists.
    table.DeleteIfExists();

## Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen des Tabellenspeichers vertraut gemacht haben, folgen Sie diesen Links, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.

<ul>
<li>In der Referenzdokumentation für den Tabellenspeicherdienst finden Sie alle Details zu verfügbaren APIs:
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Referenz zur Storage-Clientbibliothek für .NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/library/azure/dd179355">REST-API-Referenz</a></li>
  </ul>
</li>
<li>Weitere Informationen zu fortgeschrittenen Aufgaben mit Azure Storage finden Sie unter <a href="http://msdn.microsoft.com/library/azure/gg433040.aspx">Speichern von und Zugreifen auf Daten in Azure</a>.</li>
<li>Erfahren Sie, wie Sie mithilfe des <a href="../websites-dotnet-webjobs-sdk/">Azure WebJobs SDK den geschriebenen Code so vereinfachen, dass er mit Azure-Speicher funktioniert.</li>
<li>Weitere Informationen zu zusätzlichen Optionen für das Speichern von Daten in Azure finden Sie in den anderen Featureleitfäden.
  <ul>
    <li>Verwenden Sie den <a href="/documentation/articles/storage-dotnet-how-to-use-blobs/">Blobspeicher</a> zum Speichern unstrukturierter Daten.</li>
    <li>Verwenden Sie den <a href="/documentation/articles/storage-dotnet-how-to-use-queues/">Warteschlangenspeicher</a> zum Speichern relationaler Daten.</li>
    <li>Verwenden Sie eine <a href="/documentation/articles/sql-database-dotnet-how-to-use/">SQL-Datenbank</a> zum Speichern relationaler Daten.</li>
  </ul>
</li>
</ul>

  [Herunterladen und Installieren von Azure SDK für .NET]: /develop/net/
  [Erstellen eines Azure-Projekts in Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx
  
  [Blob5]: ./media/storage-dotnet-how-to-use-table-storage/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-table-storage/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-table-storage/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-table-storage/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-table-storage/blob9.png
  
  [Blogbeitrag]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
  [Referenz zur .NET-Clientbibliothek]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Speichern von und Zugreifen auf Daten in Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
  [Azure Storage-Teamblog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Konfigurieren von Verbindungszeichenfolgen]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
  [Vorgehensweise: Programmgesteuerter Zugriff auf den Tabellenspeicher]: #tablestorage

<!--HONumber=49--> 