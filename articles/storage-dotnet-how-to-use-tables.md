<properties linkid="dev-net-how-to-table-services" urlDisplayName="Table Service" pageTitle="How to use table storage  from .NET | Microsoft Azure" metaKeywords="Get started Azure table   Azure nosql   Azure large structured data store   Azure table   Azure table storage   Azure table .NET   Azure table storage .NET   Azure table C#   Azure table storage C#" description="Learn how to use Microsoft Azure Table storage to create and delete tables and insert and query entities in a table." services="storage" documentationCenter=".NET" metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="How to use Microsoft Azure Table storage" authors="tamram" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tamram"></tags>

# Verwenden des Tabellenspeichers mit .NET

Diese Anleitung beschreibt die Ausführung gängiger Szenarien mithilfe des
Azure-Tabellenspeicherdiensts. Die Beispiele sind in C# geschrieben
und verwenden die Azure Speicher-Clientbibliothek für .NET. Behandelt werden unter anderem die **Erstellung und
Löschung von Tabellen** sowie die **Arbeit mit Tabellenentitäten**. Weitere
Informationen zu Tabellen finden Sie im Abschnitt [nächste Schritte][].

> [WACOM.NOTE] Diese Anleitung gilt für die Azure Speicher-Clientbibliothek 2.x und neuere Versionen. Die empfohlene Version für die Speicher-Clientbibliothek ist 4.x. Diese Version ist entweder über [NuGet][] oder als Teil des [Azure SDK für .NET][] erhältlich. Siehe [Gewusst wie: Programmgesteuerter Zugriff auf den Tabellenspeicher][] weiter unten für weitere Informationen zum Download der Speicher-Clientbibliothek.

## Inhaltsverzeichnis

-   [Was ist der Tabellendienst?][]
-   [Konzepte][]
-   [Erstellen eines Azure-Speicherkontos][]
-   [Einrichten einer Speicherverbindungszeichenfolge][]
-   [Gewusst wie: Programmgesteuerter Zugriff auf den Tabellenspeicher][]
-   [Gewusst wie: Erstellen einer Tabelle][]
-   [Gewusst wie: Hinzufügen einer Entität zu einer Tabelle][]
-   [Gewusst wie: Einfügen eines Entitätsbatchs][]
-   [Gewusst wie: Abrufen aller Entitäten in einer Partition][]
-   [Gewusst wie: Abrufen eines Entitätsbereichs in einer Partition][]
-   [Gewusst wie: Abrufen einer einzelnen Entität][]
-   [Gewusst wie: Ersetzen einer Entität][]
-   [Gewusst wie: Einfügen oder Ersetzen einer Entität][]
-   [Gewusst wie: Abfragen einer Teilmenge von Entitätseigenschaften][]
-   [Gewusst wie: Löschen einer Entität][]
-   [Gewusst wie: Löschen einer Tabelle][]
-   [Nächste Schritte][nächste Schritte]

[WACOM.INCLUDE [howto-table-storage][]]

## 

## <a name="create-account"></a><span class="short-header">Erstellen eines Kontos</span>Erstellen eines Azure-Speicherkontos

</h2>
[WACOM.INCLUDE [create-storage-account][]]

## 

## <a name="setup-connection-string"></a><span class="short-header">Einrichten einer Verbindungszeichenfolge</span>Einrichten einer Speicherverbindungszeichenfolge

</h2>
[WACOM.INCLUDE [storage-configure-connection-string][]]

## 

## <a name="configure-access"> </a><span class="short-header">Programmgesteuerter Zugriff</span>Gewusst wie: Programmgesteuerter Zugriff auf den Tabellenspeicher

</h2>
### Abrufen der Assembly

Sie können die Assembly `Microsoft.WindowsAzure.Storage.dll` mit NuGet abrufen. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Manage NuGet Packages** aus. Suchen Sie online nach "WindowsAzure.Storage", und klicken Sie auf **Installieren**, um das Azure-Speicherpaket und die zugehörigen Abhängigkeiten zu installieren.

`Microsoft.WindowsAzure.Storage.dll` ist ebenfalls im Azure SDK für .NET enthalten, das Sie im [.NET Developer Center][] herunterladen können. Die Assembly wird im Verzeichnis `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\` installiert.

### Namespace-Deklarationen

Fügen Sie die folgenden Namespace-Deklarationen am Anfang aller C# Dateien hinzu,
in denen Sie auf den Azure-Speicher zugreifen möchten:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;

Stellen Sie sicher, dass auf die `Microsoft.WindowsAzure.Storage.dll`-Assembly verwiesen wird.

### Abrufen der Verbindungszeichenfolge

Sie können den Typ **CloudStorageAccount** verwenden, um Ihre
Speicherkontodaten abzubilden. Falls Sie eine
Azure-Projektvorlage verwenden und/oder einen Verweis auf den Namespace
Microsoft.WindowsAzure.CloudConfigurationManager haben, können Sie
den Typ **CloudConfigurationManager** verwenden,
um Ihre Speicherverbindungszeichenfolge und
Speicherkontodaten aus der Azure-Dienstkonfiguration abzurufen:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Wenn Sie eine Anwendung ohne einen Verweis auf Microsoft.WindowsAzure.CloudConfigurationManager"stellen und die Verbindungszeichenfolge sich in `web.config` oder `app.config` befindet, wie oben gezeigt, können Sie die Verbindungszeichenfolge mit **ConfigurationManager** abrufen. Sie müssen einen Verweis auf "System.Configuration.dll" zu Ihrem Projekt und eine andere Namespace-Deklaration dafür hinzufügen:

    using System.Configuration;
    ...
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

### ODataLib-Abhängigkeiten

ODataLib-Abhängigkeiten in der Speicherclientbibliothek für .NET werden durch die ODataLib-Pakete (Version 5.0.2), die über NuGet verfügbar sind, und nicht durch WCF Data Services aufgelöst. Die ODataLib-Bibliotheken können direkt heruntergeladen werden, oder es wird über Ihr Codeprojekt durch NuGet darauf verwiesen. Die spezifischen ODataLib-Pakete sind [OData][], [Edm][] und [Spatial][].

## <a name="create-table"></a><span class="short-header">Erstellen einer Tabelle</span>Gewusst wie: Erstellen einer Tabelle

Mit einem **CloudTableClient**-Objekt können Sie Referenzobjekte für
Tabellen und Entitäten abrufen. Der folgende Code erstellt ein **CloudTableClient**-Objekt
und verwendet das Objekt für die Erstellung einer neuen Tabelle. Der Code in dieser Anleitung setzt voraus,
dass es sich bei der erstellten Anwendung um ein Azure-Clouddienstprojekt handelt und
dass die Speicher-Verbindungszeichenfolge in der Dienstkonfiguration der Azure-Anwendung gespeichert ist.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the table if it doesn't exist.
    CloudTable table = tableClient.GetTableReference("people");
    table.CreateIfNotExists();

## <a name="add-entity"></a><span class="short-header">Hinzufügen einer Entität zu einer Tabelle</span>Gewusst wie: Hinzufügen einer Entität zu einer Tabelle

Entitäten werden mithilfe einer aus
**TableEntity** abgeleiteten benutzerdefinierten Klasse C#-Objekten zugeordnet. Um eine Entität zu einer Tabelle hinzuzufügen, erstellen Sie eine
Klasse, die die Eigenschaften Ihrer Entität definiert. Der folgenden Code
definiert eine Entitätsklasse, die den Vornamen des Kunden als Zeilenschlüssel
und den Nachnamen als Partitionsschlüssel verwendet. Durch den Partitions-
und den Zeilenschlüssel wird eine Entität in der Tabelle eindeutig identifiziert. Entitäten mit gleichem
Partitionsschlüssel können schneller abgefragt werden als Entitäten mit unterschiedlichem
Partitionsschlüssel. Mit unterschiedlichen Partitionsschlüsseln können
Sie allerdings die Skalierbarkeit paralleler Operationen verbessern. Bei jeder Eigenschaft, die im Tabellendienst gespeichert werden soll,
muss es sich um eine öffentliche Eigenschaft eines unterstützten Typs handeln, die sowohl `get` als auch `set` verfügbar macht.
Darüber hinaus *muss* der Entitätstyp einen parameterlosen Konstruktor verfügbar machen.

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

Tabellenvorgänge, die Entitäten umfassen, werden mit dem **CloudTable**
-Objekt durchgeführt, das Sie unter "Gewusst wie: Erstellen einer Tabelle" erstellt haben. Die auszuführende Operation
wird durch ein **TableOperation**-Objekt dargestellt. Im folgenden Codebeispiel wird die Erstellung des **CloudTable**-Objekts und danach eines **CustomerEntity**-Objekts gezeigt. Um den Vorgang vorzubereiten, wird eine **TableOperation** zum Einfügen der Kundenentität in die Tabelle erstellt. Schließlich wird der Vorgang durch den Aufruf von **CloudTable.Execute** ausgeführt.

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

## <a name="insert-batch"></a><span class="short-header">Einfügen eines Entitätsbatchs</span>Gewusst wie: Einfügen eines Entitätsbatchs

Sie können einen Stapel von Entitäten in einem einzigen Schreibvorgang in eine
Tabelle einfügen. Weitere Hinweise zu
Batchvorgängen:

1.  Sie können Aktualisierungs-, Lösch- und Einfügevorgänge in einem einzigen Batchvorgang ausführen.
2.  Ein einzelner Batchvorgang kann bis zu 100 Entitäten umfassen.
3.  Alle Entitäten in einem einzigen Batchvorgang müssen denselben
    Partitionsschlüssel haben.
4.  Eine Abfrage kann als Batchvorgang durchgeführt werden, dabei muss es sich jedoch um den einzigen Vorgang im Batch handeln.

<!-- -->

Der folgende Beispielcode erstellt zwei Entitätsobjekte und fügt beide
mithilfe der **Insert**-Methode zu einer **TableBatchOperation** hinzu. Danach wird **CloudTable.Execute** aufgerufen, um den Vorgang auszuführen.

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

## <a name="retrieve-all-entities"></a><span class="short-header">Abrufen aller Entitäten</span>Gewusst wie: Abrufen aller Entitäten in einer Partition

Verwenden Sie ein **TableQuery**-Objekt, um alle Entitäten in einer Partition aus einer Tabelle abzufragen.
Der folgende Codebeispiel definiert einen Filter für Entitäten mit 'Smith'
als Partitionsschlüssel. Dieses Beispiel gibt die Felder der einzelnen
Entitäten aus dem Abfrageergebnis in der Konsole aus.

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

## <a name="retrieve-range-entities"></a><span class="short-header">Abrufen eines Entitätsbereichs</span>Gewusst wie: Abrufen eines Entitätsbereichs in einer Partition

Falls Sie nicht alle Entitäten in einer Partition abfragen möchten,
können Sie Filter für Partitions- und Zeilenschlüssel kombinieren, um einen Bereich anzugeben. Das folgende Codebeispiel
verwendet zwei Filter, um alle Entitäten aus der Partition 'Smith' abzurufen, deren
Zeilenschlüssel (Vorname) mit einem Buchstaben beginnt, der im Alphabet vor 'E' kommt,
und gibt die Abfrageergebnisse anschließend aus.

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

## <a name="retrieve-single-entity"></a><span class="short-header">Abrufen einer einzelnen Entität</span>Gewusst wie: Abrufen einer einzelnen Entität

Sie können eine Abfrage schreiben, um eine bestimmte Entität abzurufen. Der
folgende Collections verwendet eine **TableOperation**, um den Kunden 'Ben Smith' anzugeben.
Diese Methode gibt nur eine Entität anstatt einer
Sammlung zurück, und der zurückgegebene Wert in **TableResult.Result** ist eine **CustomerEntity**.
Die Angabe von Partitions- und Zeilenschlüsseln in einer Abfrage ist der schnellste Weg,
um eine einzelne Entität aus dem Tabellenspeicherdienst abzufragen.

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

## <a name="replace-entity"></a><span class="short-header">Ersetzen einer Entität</span>Gewusst wie: Ersetzen einer Entität

Um eine Entität zu bearbeiten, rufen Sie die Entität aus dem Tabellenspeicherdienst ab, ändern das
Entitätsobjekt und speichern die Änderungen anschließend wieder im Tabellenspeicherdienst. Der
folgende Code ändert die Telefonnummer eines Bestandskunden. Anstatt
**Insert** aufzurufen, verwendet dieser Code
**Replace**. Auf diese Weise wird die Entität auf dem Server komplett ersetzt,
es sei denn, die Entität wurde seit dem Abrufen auf dem Server geändert. In diesem
Fall schlägt die Operation fehl. So wird verhindert, dass Ihre Anwendung
versehentlich Änderungen überschreibt, die seit dem Abrufen der Entität
 durch andere Komponenten Ihrer Anwendung vorgenommen wurden. In diesem Fall sollten Sie
die Entität erneut abrufen, Ihre Änderungen vornehmen (falls diese noch gültig sind), und anschließend
eine neue **Replace**-Operation ausführen. Im nächsten Abschnitt erfahren Sie,
wie Sie dieses Verhalten überschreiben können.

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

## <a name="insert-or-replace-entity"></a><span class="short-header">Einfügen oder Ersetzen einer Entität</span>Gewusst wie: Einfügen oder Ersetzen einer Entität

**Ersetzungsvorgänge** sind nicht erfolgreich, wenn die Entität seit dem
letzten Abruf vom Server geändert wurde. Außerdem müssen Sie
die Entität zunächst vom Server abrufen, um die **Replace**-Operation ausführen zu können.
Es kann jedoch vorkommen, dass Sie nicht wissen, ob die Entität auf dem Server existiert
und die enthaltenen Werte irrelevant sind - Ihr Update soll
die Werte einfach komplett überschreiben. Dazu würden Sie einen **InsertOrReplace**
-Vorgang verwenden. Diese Operation fügt die Entität ein, falls diese nicht existiert,
oder ersetzt sie andernfalls, unabhängig vom Zeitpunkt der letzten Änderung. Im folgenden
Codebeispiel wird die Kundenentität für Ben Smith ebenfalls abgerufen, anschließend jedoch mithilfe von **InsertOrReplace** auf dem Server gespeichert. Alle Änderungen
an der Entität zwischen dem Abrufen und der Update-Operation werden
überschrieben.

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

## <a name="query-entity-properties"></a><span class="short-header">Abfragen einer Teilmenge von Eigenschaften</span>Gewusst wie: Abfragen einer Teilmenge von Eigenschaften

Mit einer Tabellenabfrage können nicht nur alle, sondern auch nur einige Eigenschaften aus einer Entität abgerufen werden. Bei dieser Methode, der sogenannten Projektion, wird die Bandbreite reduziert und die Abfrageleistung gesteigert, vor allem bei großen Entitäten. Die Abfrage im
folgenden Code gibt nur die E-Mail-Adressen von Entitäten in der
Tabelle zurück. Dazu wird eine Abfrage von **DynamicTableEntity** sowie
ein **EntityResolver** verwendet. Weitere Informationen zur Projektion finden Sie in diesem [Blogbeitrag][]. Beachten Sie, dass die Projektion nicht auf dem lokalen Speicheremulator unterstützt wird und dieser Code deshalb nur bei der Verwendung eines Kontos für den Tabellendienst ausgeführt wird.

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

## <a name="delete-entity"></a><span class="short-header">Löschen einer Entität</span>Gewusst wie: Löschen einer Entität

Sie können Entitäten nach dem Abrufen löschen, indem Sie dieselben Schritte
wie beim Ändern einer Entität ausführen. Der folgende Code ruft eine
Kundenentität ab und löscht die Entität anschließend.

    // Retrieve storage account from connection string
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

## <a name="delete-table"></a><span class="short-header">Löschen einer Tabelle</span>Gewusst wie: Löschen einer Tabelle

Schließlich wird mit dem folgenden Codebeispiel eine Tabelle aus einem Speicherkonto gelöscht. Gelöschte
Tabellen können nach der Löschung für einen gewissen Zeitraum nicht
erneut erstellt werden.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Delete the table it if exists.
    table.DeleteIfExists();

## <a name="next-steps"></a><span class="short-header">Nächste Schritte</span>Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen der Tabellenspeicherung vertraut gemacht haben,
folgen Sie diesen Links, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.

-   In der Referenzdokumentation für den Tabellenspeicherdienst finden Sie alle Details zu verfügbaren APIs:
    -   [Referenz zur Speicherclientbibliothek für .NET][]
    -   [REST-API-Referenz][]
-   Weitere Informationen zu fortgeschrittenen Aufgaben mit Azure Storage finden Sie unter [Speicher][].
-   Informationen zum Arbeiten mit dem Azure-Speicher in Back-End-Prozessen für Azure-Webseiten finden Sie unter [Erste Schritte mit dem Azure WebJobs SDK][].
-   Weitere Informationen zu zusätzlichen Optionen für das Speichern von Daten in Azure finden Sie in den anderen Featureleitfäden.
    -   Verwenden Sie [Blobspeicher][] zum Speichern unstrukturierter Daten.
    -   Verwenden Sie den [Warteschlangenspeicher][] zum Speichern relationaler Daten.
    -   Verwenden Sie eine [SQL-Datenbank][] zum Speichern relationaler Daten.

  [nächste Schritte]: #next-steps
  [NuGet]: https://www.nuget.org/packages/WindowsAzure.Storage/
  [Azure SDK für .NET]: /en-us/downloads/
  [Gewusst wie: Programmgesteuerter Zugriff auf den Tabellenspeicher]: #configure-access
  [Was ist der Tabellendienst?]: #what-is
  [Konzepte]: #concepts
  [Erstellen eines Azure-Speicherkontos]: #create-account
  [Einrichten einer Speicherverbindungszeichenfolge]: #setup-connection-string
  [Gewusst wie: Erstellen einer Tabelle]: #create-table
  [Gewusst wie: Hinzufügen einer Entität zu einer Tabelle]: #add-entity
  [Gewusst wie: Einfügen eines Entitätsbatchs]: #insert-batch
  [Gewusst wie: Abrufen aller Entitäten in einer Partition]: #retrieve-all-entities
  [Gewusst wie: Abrufen eines Entitätsbereichs in einer Partition]: #retrieve-range-entities
  [Gewusst wie: Abrufen einer einzelnen Entität]: #retrieve-single-entity
  [Gewusst wie: Ersetzen einer Entität]: #replace-entity
  [Gewusst wie: Einfügen oder Ersetzen einer Entität]: #insert-or-replace-entity
  [Gewusst wie: Abfragen einer Teilmenge von Entitätseigenschaften]: #query-entity-properties
  [Gewusst wie: Löschen einer Entität]: #delete-entity
  [Gewusst wie: Löschen einer Tabelle]: #delete-table
  [howto-table-storage]: ../includes/howto-table-storage.md
  [create-storage-account]: ../includes/create-storage-account.md
  [storage-configure-connection-string]: ../includes/storage-configure-connection-string.md
  [.NET Developer Center]: http://www.windowsazure.com/en-us/develop/net/#
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
  [Blogbeitrag]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
  [Referenz zur Speicherclientbibliothek für .NET]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [REST-API-Referenz]: http://msdn.microsoft.com/en-us/library/windowsazure/dd179355
  [Speicher]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx
  [Erste Schritte mit dem Azure WebJobs SDK]: /de-de/documentation/articles/websites-dotnet-webjobs-sdk-get-started/
  [Blobspeicher]: /de-de/documentation/articles/storage-dotnet-how-to-use-blobs/
  [Warteschlangenspeicher]: /de-de/documentation/articles/storage-dotnet-how-to-use-queues/
  [SQL-Datenbank]: /de-de/documentation/articles/sql-database-dotnet-how-to-use/
