<properties
	pageTitle="Verwenden des Tabellenspeichers mit Java | Microsoft Azure"
	description="Erfahren Sie, wie Sie den Tabellenspeicherdienst in Azure verwenden. Die Codebeispiele sind in Java-Code geschrieben."
	services="storage"
	documentationCenter="java"
	authors="rmcmurray"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="12/01/2015"
	ms.author="micurd"/>


# Verwenden des Tabellenspeichers mit Java

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

## Übersicht

In diesem Leitfaden wird die Durchführung häufiger Szenarien mit dem Azure-Tabellenspeicherdienst demonstriert. Die Beispiele wurden in Java geschrieben und verwenden das [Azure Storage-SDK für Java][]. Die erläuterten Szenarien umfassten das **Erstellen**, **Auflisten** und **Löschen** von Tabellen sowie das **Einfügen**, **Abfragen**, **Ändern** und **Löschen** von Entitäten in einer Tabelle. Weitere Informationen zu Tabellen finden Sie im Abschnitt [Nächste Schritte](#NextSteps).

Hinweis: Es steht ein SDK für Entwickler zur Verfügung, die Azure Storage auf Android-Geräten verwenden. Weitere Informationen finden Sie unter [Azure Storage-SDK für Android][].

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Erstellen einer Java-Anwendung

In diesem Leitfaden werden Sie Speicherfunktionen verwenden, die lokal in einer Java-Anwendung oder in Code ausgeführt werden können, der in einer Webrolle oder in einer Workerrolle in Azure ausgeführt wird.

Dafür müssen Sie das Java Development Kit (JDK) installieren und ein Azure-Speicherkonto in Ihrem Azure-Abonnement erstellen. Sobald Sie dies erledigt haben, müssen Sie sicherstellen, dass Ihre Entwicklungssystem die minimalen Anforderungen und Abhängigkeiten erfüllt, die im Repository [Azure Storage-SDK für Java][] auf GitHub aufgelistet sind. Wenn Ihr System diese Anforderungen erfüllt, können Sie die Anweisungen für das Herunterladen und Installieren der Azure Storage-Bibliotheken für Java auf Ihr System von diesem Repository befolgen. Sobald Sie diese Aufgaben abgeschlossen haben, können Sie eine Java-Anwendung erstellen, die die Beispiele in diesem Artikel verwendet.

## Konfigurieren Ihrer Anwendung für den Zugriff auf Tabellenspeicher

Fügen Sie folgende "import"-Anweisungen am Anfang der Java-Datei dort ein, wo Microsoft Azure Storage-APIs auf Tabellen zugreifen sollen:

    // Include the following imports to use table APIs
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.table.*;
    import com.microsoft.azure.storage.table.TableQuery.*;

## Einrichten einer Azure-Speicherverbindungszeichenfolge

Ein Azure-Speicherclient verwendet eine Speicherverbindungszeichenfolge zum Speichern von Endpunkten und Anmeldeinformationen für den Zugriff auf Datenverwaltungsdienste. Bei der Ausführung in einer Clientanwendung muss die Speicherverbindungszeichenfolge in dem unten gezeigten Format angegeben werden. Dabei müssen der Name Ihres Speicherkontos und der primäre Zugriffsschlüssel für das im [Azure-Portal](https://portal.azure.com) aufgeführte Speicherkonto als Werte für *AccountName* und *AccountKey* eingegeben werden. Dieses Beispiel zeigt, wie Sie ein statisches Feld für die Verbindungszeichenfolge deklarieren:

    // Define the connection-string with your values.
    public static final String storageConnectionString =
        "DefaultEndpointsProtocol=http;" +
        "AccountName=your_storage_account;" +
        "AccountKey=your_storage_account_key";

In einer Anwendung, die in einer Microsoft Azure-Rolle ausgeführt wird, kann diese Zeichenfolge in der Dienstkonfigurationsdatei *ServiceConfiguration.cscfg* gespeichert werden. Der Zugriff darauf kann dann durch Aufruf der Methode **RoleEnvironment.getConfigurationSettings** erfolgen. Dieses Beispiel zeigt, wie die Verbindungszeichenfolge von einem **Setting**-Element mit der Bezeichnung *StorageConnectionString* in der Dienstkonfigurationsdatei abgerufen wird:

    // Retrieve storage account from connection-string.
    String storageConnectionString =
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

In den folgenden Beispielen wird davon ausgegangen, dass Sie eine dieser zwei Methoden verwendet haben, um die Speicherverbindungszeichenfolge abzurufen.

## Erstellen einer Tabelle

Mit einem **CloudTableClient**-Objekt können Sie Referenzobjekte für Tabellen und Entitäten abrufen. Der folgende Code erstellt ein **CloudTableClient**-Objekt und verwendet es zum Erstellen eines neuen **CloudTable**-Objekts, das eine Tabelle mit der Bezeichnung "people" darstellt. (Hinweis: Es gibt zusätzliche Möglichkeiten zum Erstellen von **CloudStorageAccount**-Objekten. Weitere Informationen finden Sie unter **CloudStorageAccount** in der [Azure Storage-Client-SDK-Referenz].)

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

	   // Create the table client.
	   CloudTableClient tableClient = storageAccount.createCloudTableClient();

	   // Create the table if it doesn't exist.
	   String tableName = "people";
	   CloudTable cloudTable = new CloudTable(tableName,tableClient);
	   cloudTable.createIfNotExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Auflisten der Tabellen

Rufen Sie zum Abrufen einer Liste von Tabellen die Methode **CloudTableClient.listTables()** auf, um eine wiederholbare Liste der Tabellennamen abzurufen.

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Create the table client.
    	CloudTableClient tableClient = storageAccount.createCloudTableClient();

    	// Loop through the collection of table names.
    	for (String table : tableClient.listTables())
    	{
		  // Output each table name.
		  System.out.println(table);
	   }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Hinzufügen einer Entität zu einer Tabelle

Entitäten werden mithilfe einer benutzerdefinierten Klasse, die **TableEntity** bereitstellt, Java-Objekten zugeordnet. Der Einfachheit halber stellt die **TableServiceEntity**-Klasse **TableEntity** bereit und ordnet den für die Eigenschaften genannten Getter- und Setter-Methoden durch Reflektion Eigenschaften zu. Erstellen Sie zuerst eine Klasse, mit der die Eigenschaften der Entität definiert werden, um eine Entität zu einer Tabelle hinzuzufügen. Mit dem folgenden Code wird eine Entitätsklasse definiert, die den Vornamen des Kunden als Zeilenschlüssel und den Nachnamen als Partitionsschlüssel verwendet. In Kombination miteinander wird mit dem Partitions- und Zeilenschlüssel eine Entität in der Tabelle eindeutig identifiziert. Entitäten mit demselben Partitionsschlüssel können schneller abgefragt werden als Entitäten mit verschiedenen Schlüsseln.

    public class CustomerEntity extends TableServiceEntity {
        public CustomerEntity(String lastName, String firstName) {
            this.partitionKey = lastName;
            this.rowKey = firstName;
        }

        public CustomerEntity() { }

        String email;
        String phoneNumber;

        public String getEmail() {
            return this.email;
        }

        public void setEmail(String email) {
            this.email = email;
        }

        public String getPhoneNumber() {
            return this.phoneNumber;
        }

        public void setPhoneNumber(String phoneNumber) {
            this.phoneNumber = phoneNumber;
        }
    }

Tabellenvorgänge, die Entitäten umfassen, benötigen ein **TableOperation**-Objekt. Dieses Objekt definiert den für eine Entität vorgesehenen Vorgang, der mit einem **CloudTable**-Objekt ausgeführt werden kann. Durch den folgenden Code wird eine neue Instanz der **CustomerEntity**-Klasse erstellt, wobei einige Kundendaten gespeichert werden sollen. Als Nächstes ruf der Code **TableOperation.insertOrReplace** auf, um ein **TableOperation**-Objekt für das Einfügen einer Entität in eine Tabelle zu erstellen, und weist dieser Entität die neue **CustomerEntity** zu. Abschließend ruft der Code die **execute**-Methode des **CloudTable**-Objekts auf und gibt dabei die Tabelle "people" und die neue **TableOperation** an, die dann eine Anforderung an den Speicherdienst sendet, die neue Kundenentität in die Tabelle "people" einzufügen oder die Entität zu ersetzen, wenn sie bereits vorhanden ist.

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Create the table client.
    	CloudTableClient tableClient = storageAccount.createCloudTableClient();

    	// Create a cloud table object for the table.
    	CloudTable cloudTable = tableClient.getTableReference("people");

    	// Create a new customer entity.
    	CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    	customer1.setEmail("Walter@contoso.com");
    	customer1.setPhoneNumber("425-555-0101");

    	// Create an operation to add the new customer to the people table.
    	TableOperation insertCustomer1 = TableOperation.insertOrReplace(customer1);

    	// Submit the operation to the table service.
    	cloudTable.execute(insertCustomer1);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Einfügen eines Entitätsbatchs

Sie können einen Entitätsbatch in einem Schreibvorgang in den Tabellendienst einfügen. Durch den folgenden Code wird ein **TableBatchOperation**-Objekt erstellt, zu dem anschließend drei Einfügevorgänge hinzugefügt werden. Für jeden Einfügevorgang wird ein neues Entitätsobjekt erstellt, dessen Werte werden festgelegt, und dann wird die Methode **insert** für das **TableBatchOperation**-Objekt aufgerufen und die Entität einem neuen Einfügevorgang zugeordnet. Anschließend ruft der Code die **execute**-Methode für das **CloudTable**-Objekt auf und gibt dabei die Tabelle "people" und das **TableBatchOperation**-Objekt an, das dann den Batch mit Tabellenvorgängen in einer einzelnen Anforderung an den Speicherdienst sendet.

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Create the table client.
    	CloudTableClient tableClient = storageAccount.createCloudTableClient();

    	// Define a batch operation.
    	TableBatchOperation batchOperation = new TableBatchOperation();

    	// Create a cloud table object for the table.
    	CloudTable cloudTable = tableClient.getTableReference("people");

    	// Create a customer entity to add to the table.
    	CustomerEntity customer = new CustomerEntity("Smith", "Jeff");
    	customer.setEmail("Jeff@contoso.com");
    	customer.setPhoneNumber("425-555-0104");
    	batchOperation.insertOrReplace(customer);

	   // Create another customer entity to add to the table.
	   CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
	   customer2.setEmail("Ben@contoso.com");
	   customer2.setPhoneNumber("425-555-0102");
	   batchOperation.insertOrReplace(customer2);

	   // Create a third customer entity to add to the table.
	   CustomerEntity customer3 = new CustomerEntity("Smith", "Denise");
	   customer3.setEmail("Denise@contoso.com");
	   customer3.setPhoneNumber("425-555-0103");
	   batchOperation.insertOrReplace(customer3);

	   // Execute the batch of operations on the "people" table.
	   cloudTable.execute(batchOperation);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

Beachten Sie im Zusammenhang mit Batchvorgängen Folgendes:

- Bis zu 100 Einfüge-, Lösch-, Zusammenführungs-, Ersetzungs-, Einfüge- oder Zusammenführungs- und Einfüge- oder Ersetzungsvorgänge können in beliebiger Kombination in einem einzelnen Batch ausgeführt werden.
- Ein Batchvorgang kann einen Abfragevorgang enthalten, allerdings nur als einzigen Vorgang in diesem Batch.
- Alle Entitäten in einem Batchvorgang müssen über denselben Partitionsschlüssel verfügen.
- Ein Batchvorgang ist auf eine Nutzlast von 4 MB Daten beschränkt.

## Abrufen aller Entitäten in einer Partition

Verwenden Sie ein **TableQuery**-Objekt, um eine Tabelle für Entitäten in einer Partition abzurufen. Rufen Sie **TableQuery.from** auf, um eine Abfrage in einer bestimmten Tabelle zu erstellen, die einen bestimmen Ergebnistyp übergibt. Im folgenden Code wird ein Filter für Entitäten erstellt, wobei "Smith" der Partitionsschlüssel ist. **TableQuery.generateFilterCondition** ist eine Helper-Methode für die Erstellung von Filtern für Abfragen. Rufen Sie **where** auf der von der **TableQuery.from**-Methode übergebenen Referenz auf, um den Filter auf die Abfrage anzuwenden. Wenn die Abfrage mit einem Aufruf der **execute**-Methode für das **CloudTable**-Objekt ausgeführt wird, übergibt sie einen **Iterator** mit **CustomerEntity** als Ergebnistyp. Anschließend können Sie den übergebenen **Iterator** verwenden, um die Ergebnisse in jede Schleife einzubinden. In diesem Code werden die Felder der einzelnen Entitäten in den Abfrageergebnissen an die Konsole ausgegeben.

    try
    {
    	// Define constants for filters.
    	final String PARTITION_KEY = "PartitionKey";
    	final String ROW_KEY = "RowKey";
    	final String TIMESTAMP = "Timestamp";

    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Create the table client.
    	CloudTableClient tableClient = storageAccount.createCloudTableClient();

	   // Create a cloud table object for the table.
	   CloudTable cloudTable = tableClient.getTableReference("people");

    	// Create a filter condition where the partition key is "Smith".
    	String partitionFilter = TableQuery.generateFilterCondition(
	       PARTITION_KEY,
	       QueryComparisons.EQUAL,
	       "Smith");

	   // Specify a partition query, using "Smith" as the partition key filter.
	   TableQuery<CustomerEntity> partitionQuery =
	       TableQuery.from(CustomerEntity.class)
	       .where(partitionFilter);

        // Loop through the results, displaying information about the entity.
        for (CustomerEntity entity : cloudTable.execute(partitionQuery)) {
            System.out.println(entity.getPartitionKey() +
                " " + entity.getRowKey() +
                "\t" + entity.getEmail() +
                "\t" + entity.getPhoneNumber());
	   }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Abrufen eines Entitätsbereichs in einer Partition

Wenn Sie nicht alle Entitäten in einer Partition abrufen möchten, können Sie durch Verwendung von Vergleichsoperatoren einen Bereich angeben. Im folgenden Code werden zwei Filter kombiniert, um alle Entitäten in der Partition "Smith" abzurufen, deren Zeilenschlüssel (Vorname) mit einem Buchstaben vor dem Buchstaben "E" im Alphabet beginnen. Danach werden die Abfrageergebnisse ausgegeben. Wenn Sie die Entitäten verwenden, die in diesem Leitfaden im Kapitel zum Batcheinfügevorgang in die Tabelle eingefügt wurden, werden in diesem Fall nur zwei Entitäten übergeben (Ben und Denise Smith); Jeff Smith ist nicht enthalten.

    try
    {
    	// Define constants for filters.
    	final String PARTITION_KEY = "PartitionKey";
    	final String ROW_KEY = "RowKey";
    	final String TIMESTAMP = "Timestamp";

    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

	   // Create the table client.
	   CloudTableClient tableClient = storageAccount.createCloudTableClient();

	   // Create a cloud table object for the table.
	   CloudTable cloudTable = tableClient.getTableReference("people");

    	// Create a filter condition where the partition key is "Smith".
    	String partitionFilter = TableQuery.generateFilterCondition(
	       PARTITION_KEY,
	       QueryComparisons.EQUAL,
	       "Smith");

    	// Create a filter condition where the row key is less than the letter "E".
    	String rowFilter = TableQuery.generateFilterCondition(
	       ROW_KEY,
	       QueryComparisons.LESS_THAN,
	       "E");

    	// Combine the two conditions into a filter expression.
    	String combinedFilter = TableQuery.combineFilters(partitionFilter,
	        Operators.AND, rowFilter);

    	// Specify a range query, using "Smith" as the partition key,
    	// with the row key being up to the letter "E".
    	TableQuery<CustomerEntity> rangeQuery =
	       TableQuery.from(CustomerEntity.class)
	       .where(combinedFilter);

    	// Loop through the results, displaying information about the entity
        for (CustomerEntity entity : cloudTable.execute(rangeQuery)) {
            System.out.println(entity.getPartitionKey() +
                " " + entity.getRowKey() +
                "\t" + entity.getEmail() +
                "\t" + entity.getPhoneNumber());
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Abrufen einer einzelnen Entität

Sie können eine Abfrage schreiben, um eine einzelne bestimmte Entität abzurufen. Im folgenden Code wird der Kunde "Jeff Smith" nicht durch Erstellen einer **TableQuery** und Verwendung von Filtern übergeben, sondern durch Aufruf von **TableOperation.retrieve** mit Partitions- und Zeilenschlüsselparametern. Bei der Ausführung übergibt dieser Abfragevorgang nicht eine ganze Sammlung von Entitäten, sondern nur eine einzelne Entität. Die Methode **getResultAsType** wandelt das Ergebnis in den Typ des Zuweisungsziels um, ein **CustomerEntity**-Objekt. Wenn dieser Typ mit dem für die Abfrage spezifizierten Typ nicht kompatibel ist, wird eine Ausnahme ausgelöst. Ein Nullwert wird übergeben, wenn keine Entität eine übereinstimmende Partition und einen passenden Zeilenschlüssel hat. Die Angabe beider Schlüssel, Partition und Zeile, in einer Abfrage ist die schnellste Möglichkeit, um eine einzelne Entität aus dem Tabellendienst abzurufen.

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Create the table client.
    	CloudTableClient tableClient = storageAccount.createCloudTableClient();

    	// Create a cloud table object for the table.
    	CloudTable cloudTable = tableClient.getTableReference("people");

    	// Retrieve the entity with partition key of "Smith" and row key of "Jeff"
    	TableOperation retrieveSmithJeff =
	       TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

	   // Submit the operation to the table service and get the specific entity.
	   CustomerEntity specificEntity =
    		cloudTable.execute(retrieveSmithJeff).getResultAsType();

    	// Output the entity.
    	if (specificEntity != null)
    	{
            System.out.println(specificEntity.getPartitionKey() +
                " " + specificEntity.getRowKey() +
                "\t" + specificEntity.getEmail() +
                "\t" + specificEntity.getPhoneNumber());
	   }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Ändern einer Entität

Um eine Entität zu ändern, rufen Sie sie aus dem Tabellendienst ab, ändern Sie das Entitätsobjekt, und speichern Sie die Änderungen dann mit einem Ersetzungs- oder Zusammenführungsvorgang im Tabellendienst. Mit dem folgenden Code wird die Telefonnummer eines vorhandenen Kunden geändert. In diesem Fall wird nicht wie beim Einfügen **TableOperation.insert**, sondern **TableOperation.replace** aufgerufen. Die Methode **CloudTable.execute** ruft den Tabellendienst auf, und die Entität wird ersetzt, sofern sie nicht nach dem Aufruf durch diese Anwendung in der Zwischenzeit von einer anderen Anwendung geändert wurde. In diesem Fall wird eine Ausnahme ausgelöst, und die Entität muss erneut aufgerufen, geändert und gespeichert werden. Dieses auf dem "Optimistic Concurrency"-Verfahren (optimistisches Locking) basierende Muster für Wiederholungsversuche ist in verteilten Speichersystemen häufig anzutreffen.

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Create the table client.
    	CloudTableClient tableClient = storageAccount.createCloudTableClient();

    	// Create a cloud table object for the table.
    	CloudTable cloudTable = tableClient.getTableReference("people");

    	// Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    	TableOperation retrieveSmithJeff =
	       TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    	// Submit the operation to the table service and get the specific entity.
    	CustomerEntity specificEntity =
		  cloudTable.execute(retrieveSmithJeff).getResultAsType();

    	// Specify a new phone number.
    	specificEntity.setPhoneNumber("425-555-0105");

    	// Create an operation to replace the entity.
    	TableOperation replaceEntity = TableOperation.replace(specificEntity);

    	// Submit the operation to the table service.
    	cloudTable.execute(replaceEntity);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Abfragen einer Teilmenge von Entitätseigenschaften

Mit einer Abfrage einer Tabelle können nur einige wenige Eigenschaften einer Entität aufgerufen werden. Bei dieser Methode, der sogenannten Projektion, wird die Bandbreite reduziert und die Abfrageleistung gesteigert, vor allem bei großen Entitäten. Die Abfrage im folgenden Code übergibt mit der Methode **select** nur die E-Mail-Adressen von Entitäten in der Tabelle. Die Ergebnisse werden mit Unterstützung eines **EntityResolver**, der die Typumwandlung der vom Server übergebenen Entitäten übernimmt, in eine Sammlung von **String**-Objekten projiziert. Weitere Informationen zur Projektion finden Sie in diesem [Blogbeitrag][]. Beachten Sie, dass die Projektion nicht auf dem lokalen Speicheremulator unterstützt wird und dieser Code deshalb nur bei der Verwendung eines Kontos für den Tabellendienst ausgeführt wird.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

    	// Create the table client.
    	CloudTableClient tableClient = storageAccount.createCloudTableClient();

    	// Create a cloud table object for the table.
    	CloudTable cloudTable = tableClient.getTableReference("people");

    	// Define a projection query that retrieves only the Email property
    	TableQuery<CustomerEntity> projectionQuery =
	       TableQuery.from(CustomerEntity.class)
	       .select(new String[] {"Email"});

    	// Define a Entity resolver to project the entity to the Email value.
    	EntityResolver<String> emailResolver = new EntityResolver<String>() {
            @Override
            public String resolve(String PartitionKey, String RowKey, Date timeStamp, HashMap<String, EntityProperty> properties, String etag) {
                return properties.get("Email").getValueAsString();
            }
        };

        // Loop through the results, displaying the Email values.
        for (String projectedString :
            cloudTable.execute(projectionQuery, emailResolver)) {
                System.out.println(projectedString);
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Einfügen oder Ersetzen einer Entität

Es kommt häufig vor, dass Sie eine Entität in eine Tabelle einfügen möchten, ohne zuvor prüfen zu müssen, ob diese Entität bereits in der Tabelle vorhanden ist. Mit einem Einfügen-oder-Ersetzen-Vorgang genügt eine einzelne Anforderung, um eine nicht vorhandene Entität einzufügen oder eine vorhandene zu ersetzen. Ausgehend von vorherigen Beispielen wird durch diesen Code die Entität für "Walter Harp" eingefügt oder ersetzt. Nachdem eine neue Entität erstellt worden ist, ruft dieser Code die Methode **TableOperation.insertOrReplace** auf. Anschließend ruft der Code die Methode **execute** für das **CloudTable**-Objekt auf und spezifiziert als Parameter die Tabelle und den Einfügen-oder-Ersetzen-Vorgang. Wenn nur ein Teil einer Entität aktualisiert werden sollen, kann stattdessen die Methode **TableOperation.insertOrMerge** verwendet werden. Hinweis: Einfügen-oder-Ersetzen-Vorgänge werden auf dem lokalen Speicheremulator nicht unterstützt, weshalb dieser Code nur bei Verwendung eines Kontos auf dem Tabellendienst ausgeführt wird. Weitere Informationen zu Einfügen-oder-Ersetzen- und Einfügen-oder-Zusammenfügen-Vorgängen finden Sie in diesem [Blogbeitrag][].

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Create a new customer entity.
        CustomerEntity customer5 = new CustomerEntity("Harp", "Walter");
        customer5.setEmail("Walter@contoso.com");
        customer5.setPhoneNumber("425-555-0106");

        // Create an operation to add the new customer to the people table.
        TableOperation insertCustomer5 = TableOperation.insertOrReplace(customer5);

        // Submit the operation to the table service.
        cloudTable.execute(insertCustomer5);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Löschen einer Entität

Sie können eine Entität problemlos nach dem Abrufen löschen. Wenn die Entität aufgerufen worden ist, rufen Sie die **TableOperation.delete**-Methode mit der zu löschenden Entität auf. Rufen Sie dann **execute** für das **CloudTable**-Objekt auf. Durch den nachstehenden Code wird eine Kundenentität aufgerufen und gelöscht.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
        TableOperation retrieveSmithJeff = TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

        // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
        CustomerEntity entitySmithJeff =
            cloudTable.execute(retrieveSmithJeff).getResultAsType();

        // Create an operation to delete the entity.
        TableOperation deleteSmithJeff = TableOperation.delete(entitySmithJeff);

        // Submit the delete operation to the table service.
        cloudTable.execute(deleteSmithJeff);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Löschen einer Tabelle

Schließlich wird mit dem folgenden Code eine Tabelle aus einem Speicherkonto gelöscht. Eine gelöschte Tabelle kann für eine bestimmte Zeitdauer, in der Regel maximal vierzig Sekunden, nach dem Löschvorgang nicht neu erstellt werden.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Delete the table and all its data if it exists.
        CloudTable cloudTable = new CloudTable("people",tableClient);
        cloudTable.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen der Tabellenspeicherung vertraut gemacht haben, folgen Sie diesen Links, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.

- [Azure Storage-SDK für Java][]
- [Referenz für Azure Storage-Client-SDKs][]
- [Azure Storage-REST-API][]
- [Azure Storage-Teamblog][]

Weitere Informationen finden Sie außerdem im [Java Developer Center](/develop/java/).


[Azure SDK for Java]: http://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage-SDK für Java]: https://github.com/azure/azure-storage-java
[Azure Storage-SDK für Android]: https://github.com/azure/azure-storage-android
[Azure Storage-Client-SDK-Referenz]: http://dl.windowsazure.com/storage/javadoc/
[Referenz für Azure Storage-Client-SDKs]: http://dl.windowsazure.com/storage/javadoc/
[Azure Storage-REST-API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure Storage-Teamblog]: http://blogs.msdn.com/b/windowsazurestorage/
[Blogbeitrag]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx

<!---HONumber=AcomDC_0128_2016-->