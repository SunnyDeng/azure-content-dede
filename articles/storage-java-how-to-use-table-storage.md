<properties urlDisplayName="Table Service" pageTitle="Verwenden des Tabellenspeichers (Java) | Microsoft Azure" metaKeywords="Azure table storage service, Azure table service Java, table storage Java" description="Learn how to use the table storage service in Azure. Code samples are written in Java code." metaCanonical="" services="storage" documentationCenter="Java" title="How to use the Table storage service from Java" authors="tamram" solutions="" manager="adinah" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# Verwenden des Tabellenspeichers in Java

In diesem Leitfaden wird die Durchführung häufiger Szenarien mit dem Azure-Tabellenspeicherdienst demonstriert. Die Beispiele wurden in Java geschrieben und verwenden das [Azure Storage-SDK für Java][]. Die erläuterten Szenarien umfassten das **Erstellen**, **Auflisten** und **Löschen** von Tabellen sowie das **Einfügen**, **Abfragen**, **Ändern** und **Löschen** von Entitäten in einer Tabelle. Weitere Informationen zu Tabellen finden Sie im Abschnitt [Nächste Schritte](#NextSteps).

Hinweis: Ein SDK steht für Entwickler zur Verfügung, die Azure Storage auf Android-Geräten verwenden. Weitere Informationen finden Sie unter [Azure Storage-SDK für Android][]. 

## <a name="Contents"> </a>Inhaltsverzeichnis

* [Was ist Table Storage](#what-is)
* [Konzepte](#Concepts)
* [Erstellen eines Azure-Speicherkontos](#CreateAccount)
* [Erstellen einer Java-Anwendung](#CreateApplication)
* [Konfigurieren Ihrer Anwendung für den Zugriff auf Table Storage](#ConfigureStorage)
* [Einrichten einer Azure-Speicherverbindungszeichenfolge](#ConnectionString)
* [Gewusst wie: Erstellen einer Tabelle](#CreateTable)
* [Gewusst wie: Auflisten der Tabellen](#ListTables)
* [Gewusst wie: Hinzufügen einer Entität zu einer Tabelle](#AddEntity)
* [Gewusst wie: Einfügen eines Entitätsbatchs](#InsertBatch)
* [Gewusst wie: Abrufen aller Entitäten in einer Partition](#RetrieveEntities)
* [Gewusst wie: Abrufen eines Entitätsbereichs in einer Partition](#RetrieveRange)
* [Gewusst wie: Abrufen einer einzelnen Entität](#RetriveSingle)
* [Gewusst wie: Ändern einer Entität](#ModifyEntity)
* [Gewusst wie: Abfragen einer Teilmenge von Entitätseigenschaften](#QueryProperties)
* [Gewusst wie: Einfügen oder Ersetzen einer Entität](#InsertOrReplace)
* [Gewusst wie: Löschen einer Entität](#DeleteEntity)
* [Gewusst wie: Löschen einer Tabelle](#DeleteTable)
* [Nächste Schritte](#NextSteps)

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

##<a name="CreateAccount"></a>Erstellen eines Azure-Speicherkontos

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="CreateApplication"></a>Erstellen einer Java-Anwendung

In diesem Leitfaden werden Sie Speicherfunktionen verwenden, die lokal in einer Java-Anwendung oder in Code ausgeführt werden können, der in einer Webrolle oder in einer Workerrolle in Azure ausgeführt wird.

Dafür müssen Sie das Java Development Kit (JDK) installieren und ein Azure-Speicherkonto in Ihrem Azure-Abonnement erstellen. Sobald Sie dies erledigt haben, müssen Sie sicherstellen, dass Ihre Entwicklungssystem die minimalen Anforderungen und Abhängigkeiten erfüllt, die im Repository [Azure Storage-SDK für Java][] auf GitHub aufgelistet sind. Wenn Ihr System diese Anforderungen erfüllt, können Sie die Anweisungen für das Herunterladen und Installieren der Azure Storage-Bibliotheken für Java auf Ihr System von diesem Repository befolgen. Sobald Sie diese Aufgaben abgeschlossen haben, können Sie eine Java-Anwendung erstellen, die die Beispiele in diesem Artikel verwendet.

## <a name="ConfigureStorage"> </a>Konfigurieren Ihrer Anwendung für den Zugriff auf Table Storage

Fügen Sie folgende "import"-Anweisungen am Anfang der Java-Datei dort ein, wo Windows Azure-Speicher-APIs auf Tabellen zugreifen sollen:

    // Folgende Importanweisungen für die Verwendung von Tabellen-APIs einschließen
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.table.*;
    import com.microsoft.azure.storage.table.TableQuery.*;

## <a name="ConnectionString"> </a>Einrichten einer Azure-Speicherverbindungszeichenfolge

Ein Azure-Speicherclient verwendet eine Speicherverbindungszeichenfolge zum Speichern von Endpunkten und Anmeldeinformationen für den Zugriff auf Datenverwaltungsdienste. Bei der Ausführung in einer Clientanwendung muss die Speicherverbindungszeichenfolge in dem unten gezeigten Format angegeben werden. Dabei müssen der Name Ihres Speicherkontos und der primäre Zugriffsschlüssel für das im Verwaltungsportal aufgeführte Speicherkonto als *AccountName-* und *AccountKey-* Werte eingegeben werden. Dieses Beispiel zeigt, wie Sie ein statisches Feld für die Verbindungszeichenfolge deklarieren:

    // Verbindungszeichenfolge mit Ihren Werten definieren.
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

In einer Anwendung, die in einer Microsoft Azure-Rolle ausgeführt wird, kann diese Zeichenfolge in der Dienstkonfigurationsdatei *ServiceConfiguration.cscfg* gespeichert werden. Der Zugriff darauf kann dann durch Aufruf der Methode **RoleEnvironment.getConfigurationSettings** erfolgen. Dieses Beispiel zeigt, wie die Verbindungszeichenfolge von einem **Setting**-Element mit der Bezeichnung *StorageConnectionString* in der Dienstkonfigurationsdatei abgerufen wird:

    // Speicherkonto aus Verbindungszeichenfolge abrufen.
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

In den folgenden Beispielen wird davon ausgegangen, dass Sie eine dieser zwei Methoden verwendet haben, um die Speicherverbindungszeichenfolge abzurufen.

## <a name="CreateTable"> </a>Gewusst wie: Erstellen einer Tabelle

Mit einem **CloudTableClient**-Objekt können Sie Referenzobjekte für
Tabellen und Entitäten abrufen. Mit dem folgenden Code wird ein **CloudTableClient**-Objekt erstellt,
und verwendet es zum Erstellen eines neuen **CloudTable**-Objekts, das eine Tabelle mit der Bezeichnung "people" darstellt. (Hinweis: Es gibt zusätzliche Möglichkeiten zum Erstellen von **CloudStorageAccount**-Objekten. Weitere Informationen finden Sie unter **CloudStorageAccount** im Thema zur [Azure Storage-Client-SDK-Referenz].)

    try
    {
    	// Speicherkonto aus Verbindungszeichenfolge abrufen.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

	   // Tabellenclient erstellen.
	   CloudTableClient tableClient = storageAccount.createCloudTableClient();

	   // Tabelle erstellen, wenn diese noch nicht vorhanden ist.
	   String tableName = "personen";
	   CloudTable cloudTable = new CloudTable(tableName,tableClient);
	   cloudTable.createIfNotExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="ListTables"></a>Gewusst wie: Auflisten der Tabellen

Rufen Sie zum Abrufen einer Liste von Tabellen die Methode **CloudTableClient.listTables()** auf, um eine wiederholbare Liste der Tabellennamen abzurufen.

    try
    {
    	// Speicherkonto aus Verbindungszeichenfolge abrufen.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Tabellenclient erstellen.
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

## <a name="AddEntity"> </a>Gewusst wie: Hinzufügen einer Entität zu einer Tabelle

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
    	// Speicherkonto aus Verbindungszeichenfolge abrufen.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Tabellenclient erstellen.
    	CloudTableClient tableClient = storageAccount.createCloudTableClient();
			
    	// Create a cloud table object for the table.
    	CloudTable cloudTable = tableClient.getTableReference("people");
			
    	// Neue Kundenentität erstellen.
    	CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    	customer1.setEmail("Walter@contoso.com");
    	customer1.setPhoneNumber("425-555-0101");
			
    	// Vorgang erstellen, um den neuen Kunden in die Tabelle "personen" einzufügen.
    	TableOperation insertCustomer1 = TableOperation.insertOrReplace(customer1);

    	// Den Vorgang an den Tabellendienst senden.
    	cloudTable.execute(insertCustomer1);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="InsertBatch"> </a>Gewusst wie: Einfügen eines Entitätsbatchs

Sie können einen Entitätsbatch in einem Schreibvorgang in den Tabellendienst einfügen. Durch den folgenden Code wird ein **TableBatchOperation**-Objekt erstellt, zu dem anschließend drei Einfügevorgänge hinzugefügt werden. Für jeden Einfügevorgang wird ein neues Entitätsobjekt erstellt, dessen Werte werden festgelegt, und dann wird die Methode **insert** für das **TableBatchOperation**-Objekt aufgerufen und die Entität einem neuen Einfügevorgang zugeordnet. Anschließend ruft der Code die **execute**-Methode für das **CloudTable**-Objekt auf und gibt dabei die Tabelle "people" und das **TableBatchOperation**-Objekt an, das dann den Batch mit Tabellenvorgängen in einer einzelnen Anforderung an den Speicherdienst sendet.

    try
    {
    	// Speicherkonto aus Verbindungszeichenfolge abrufen.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Tabellenclient erstellen.
    	CloudTableClient tableClient = storageAccount.createCloudTableClient();

    	// Batchvorgang definieren.
    	TableBatchOperation batchOperation = new TableBatchOperation();

    	// Create a cloud table object for the table.
    	CloudTable cloudTable = tableClient.getTableReference("people");

    	// Kundenentität erstellen und zur Tabelle hinzufügen.
    	CustomerEntity customer = new CustomerEntity("Smith", "Jeff");
    	customer.setEmail("Jeff@contoso.com");
    	customer.setPhoneNumber("425-555-0104");
    	batchOperation.insertOrReplace(customer);

	   // Zweite Kundenentität erstellen und zur Tabelle hinzufügen.
	   CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
	   customer2.setEmail("Ben@contoso.com");
	   customer2.setPhoneNumber("425-555-0102");
	   batchOperation.insertOrReplace(customer2);

	   // Dritte Kundenentität erstellen und zur Tabelle hinzufügen.
	   CustomerEntity customer3 = new CustomerEntity("Smith", "Denise");
	   customer3.setEmail("Denise@contoso.com");
	   customer3.setPhoneNumber("425-555-0103");
	   batchOperation.insertOrReplace(customer3);

	   // Vorgangsbatch für Tabelle "personen" ausführen.
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

## <a name="RetrieveEntities"> </a>Gewusst wie: Abrufen aller Entitäten in einer Partition

Verwenden Sie ein **TableQuery**-Objekt, um eine Tabelle für Entitäten in einer Partition abzurufen. Rufen Sie **TableQuery.from** auf, um eine Abfrage in einer bestimmten Tabelle zu erstellen, die einen bestimmen Ergebnistyp übergibt. Im folgenden Code wird ein Filter für Entitäten erstellt, wobei "Smith" der Partitionsschlüssel ist. **TableQuery.generateFilterCondition** ist eine Helper-Methode für die Erstellung von Filtern für Abfragen. Rufen Sie **where** auf der von der **TableQuery.from**-Methode übergebenen Referenz auf, um den Filter auf die Abfrage anzuwenden. Wenn die Abfrage mit einem Aufruf der **execute**-Methode für das **CloudTable**-Objekt ausgeführt wird, übergibt sie einen **Iterator** mit **CustomerEntity** als Ergebnistyp. Anschließend können Sie den übergebenen **Iterator** verwenden, um die Ergebnisse in jede Schleife einzubinden. In diesem Code werden die Felder der einzelnen Entitäten in den Abfrageergebnissen an die Konsole ausgegeben.

    try
    {
    	// Define constants for filters.
    	final String PARTITION_KEY = "PartitionKey";
    	final String ROW_KEY = "RowKey";
    	final String TIMESTAMP = "Timestamp";

    	// Speicherkonto aus Verbindungszeichenfolge abrufen.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Tabellenclient erstellen.
    	CloudTableClient tableClient = storageAccount.createCloudTableClient();
			
	   // Create a cloud table object for the table.
	   CloudTable cloudTable = tableClient.getTableReference("people");

    	// Filterbedingung mit dem Partitionsschlüssel "Smith" erstellen.
    	String partitionFilter = TableQuery.generateFilterCondition(
	       PARTITION_KEY, 
	       QueryComparisons.EQUAL,
	       "Smith");

	   // Partitionsabfrage mit "Smith" als Partitionsschlüsselfilter erstellen.
	   TableQuery<CustomerEntity> partitionQuery =
	       TableQuery.from(CustomerEntity.class)
	       .where(partitionFilter);

        // Schleife durch die Ergebnisse durchlaufen und Informationen zur Entität anzeigen.
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

## <a name="RetrieveRange"> </a>Gewusst wie: Abrufen eines Entitätsbereichs in einer Partition

Wenn Sie nicht alle Entitäten in einer Partition abrufen möchten, können Sie durch Verwendung von Vergleichsoperatoren einen Bereich angeben. Im folgenden Code werden zwei Filter kombiniert, um alle Entitäten in der Partition "Smith" abzurufen, deren Zeilenschlüssel (Vorname) mit einem Buchstaben vor dem Buchstaben "E" im Alphabet beginnen. Danach werden die Abfrageergebnisse ausgegeben. Wenn Sie die Entitäten verwenden, die in diesem Leitfaden im Kapitel zum Batcheinfügevorgang in die Tabelle eingefügt wurden, werden in diesem Fall nur zwei Entitäten übergeben (Ben und Denise Smith); Jeff Smith ist nicht enthalten.

    try
    {
    	// Define constants for filters.
    	final String PARTITION_KEY = "PartitionKey";
    	final String ROW_KEY = "RowKey";
    	final String TIMESTAMP = "Timestamp";
			
    	// Speicherkonto aus Verbindungszeichenfolge abrufen.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

	   // Tabellenclient erstellen.
	   CloudTableClient tableClient = storageAccount.createCloudTableClient();

	   // Create a cloud table object for the table.
	   CloudTable cloudTable = tableClient.getTableReference("people");

    	// Filterbedingung mit dem Partitionsschlüssel "Smith" erstellen.
    	String partitionFilter = TableQuery.generateFilterCondition(
	       PARTITION_KEY, 
	       QueryComparisons.EQUAL,
	       "Smith");

    	// Filterbedingung mit einem Zeilenschlüssel erstellen, der kleiner ist als die Anzahl der Buchstaben bis zum Buchstaben "E".
    	String rowFilter = TableQuery.generateFilterCondition(
	       ROW_KEY, 
	       QueryComparisons.LESS_THAN,
	       "E");

    	// Die beiden Bedingungen in einem Filterausdruck kombinieren.
    	String combinedFilter = TableQuery.combineFilters(partitionFilter, 
	        Operators.AND, rowFilter);

    	// Bereichsabfrage mit "Smith" als Partitionsschlüssel erstellen.
    	// wobei der Zeilenschlüssel nur bis zum Buchstaben "E" geht.
    	TableQuery<CustomerEntity> rangeQuery =
	       TableQuery.from(CustomerEntity.class)
	       .where(combinedFilter);

    	// Schleife durch die Ergebnisse durchlaufen und Informationen zur Entität anzeigen.
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

## <a name="RetriveSingle"> </a>Gewusst wie: Abrufen einer einzelnen Entität

Sie können eine Abfrage schreiben, um eine bestimmte Entität abzurufen. Im folgenden Code wird der Kunde "Jeff Smith" nicht durch Erstellen einer **TableQuery** und Verwendung von Filtern übergeben, sondern durch Aufruf von **TableOperation.retrieve** mit Partitions- und Zeilenschlüsselparametern. Bei der Ausführung übergibt dieser Abfragevorgang nicht eine ganze Sammlung von Entitäten, sondern nur eine einzelne Entität. Die Methode **getResultAsType** wandelt das Ergebnis in den Typ des Zuweisungsziels um, ein **CustomerEntity**-Objekt. Wenn dieser Typ mit dem für die Abfrage spezifizierten Typ nicht kompatibel ist, wird eine Ausnahme ausgelöst. Ein Nullwert wird übergeben, wenn keine Entität eine übereinstimmende Partition und einen passenden Zeilenschlüssel hat. Die Angabe beider Schlüssel, Partition und Zeile, in einer Abfrage ist die schnellste Möglichkeit, um eine einzelne Entität aus dem Tabellendienst abzurufen.

    try
    {
    	// Speicherkonto aus Verbindungszeichenfolge abrufen.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Tabellenclient erstellen.
    	CloudTableClient tableClient = storageAccount.createCloudTableClient();

    	// Create a cloud table object for the table.
    	CloudTable cloudTable = tableClient.getTableReference("people");

    	// Entität mit Partitionsschlüssel "Smith" und Zeilenschlüssel "Jeff" abrufen
    	TableOperation retrieveSmithJeff = 
	       TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

	   // Den Vorgang an den Tabellendienst senden und die betreffende Entität abrufen.
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

## <a name="ModifyEntity"> </a>Gewusst wie: Ändern einer Entität

Um eine Entität zu ändern, rufen Sie sie aus dem Tabellendienst ab, ändern Sie das Entitätsobjekt, und speichern Sie die Änderungen dann mit einem Ersetzungs- oder Zusammenführungsvorgang im Tabellendienst. Mit dem folgenden Code wird die Telefonnummer eines vorhandenen Kunden geändert. In diesem Fall wird nicht wie beim Einfügen **TableOperation.insert**, sondern **TableOperation.replace** aufgerufen. Die Methode **CloudTable.execute** ruft den Tabellendienst auf, und die Entität wird ersetzt, sofern sie nicht nach dem Aufruf durch diese Anwendung in der Zwischenzeit von einer anderen Anwendung geändert wurde. In diesem Fall wird eine Ausnahme ausgelöst, und die Entität muss erneut aufgerufen, geändert und gespeichert werden. Dieses auf dem "Optimistic Concurrency"-Verfahren (optimistisches Locking) basierende Muster für Wiederholungsversuche ist in verteilten Speichersystemen häufig anzutreffen.

    try
    {
    	// Speicherkonto aus Verbindungszeichenfolge abrufen.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Tabellenclient erstellen.
    	CloudTableClient tableClient = storageAccount.createCloudTableClient();

    	// Create a cloud table object for the table.
    	CloudTable cloudTable = tableClient.getTableReference("people");

    	// Entität mit Partitionsschlüssel "Smith" und Zeilenschlüssel "Jeff" abrufen.
    	TableOperation retrieveSmithJeff = 
	       TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    	// Den Vorgang an den Tabellendienst senden und die betreffende Entität abrufen.
    	CustomerEntity specificEntity =
		  cloudTable.execute(retrieveSmithJeff).getResultAsType();

    	// Neue Telefonnummer angeben.
    	specificEntity.setPhoneNumber("425-555-0105");

    	// Vorgang für das Ersetzen der Entität erstellen.
    	TableOperation replaceEntity = TableOperation.replace(specificEntity);

    	// Den Vorgang an den Tabellendienst senden.
    	cloudTable.execute(replaceEntity);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="QueryProperties"> </a>Gewusst wie: Abfragen einer Teilmenge von Entitätseigenschaften

Mit einer Abfrage einer Tabelle können nur einige wenige Eigenschaften einer Entität aufgerufen werden. Bei dieser Methode, der sogenannten Projektion, wird die Bandbreite reduziert und die Abfrageleistung gesteigert, vor allem bei großen Entitäten. Die Abfrage im folgenden Code übergibt mit der Methode **select** nur die E-Mail-Adressen von Entitäten in der Tabelle. Die Ergebnisse werden mit Unterstützung eines **EntityResolver**, der die Typumwandlung der vom Server übergebenen Entitäten übernimmt, in eine Sammlung von **String**-Objekten projiziert. Weitere Informationen zur Projektion finden Sie in diesem [Blogbeitrag][]. Beachten Sie, dass die Projektion nicht auf dem lokalen Speicheremulator unterstützt wird und dieser Code deshalb nur bei der Verwendung eines Kontos für den Tabellendienst ausgeführt wird.

    try
    {
        // Speicherkonto aus Verbindungszeichenfolge abrufen.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

    	// Tabellenclient erstellen.
    	CloudTableClient tableClient = storageAccount.createCloudTableClient();

    	// Create a cloud table object for the table.
    	CloudTable cloudTable = tableClient.getTableReference("people");

    	// Projektionsabfrage definieren, die nur die E-Mail-Eigenschaft abruft
    	TableQuery<CustomerEntity> projectionQuery = 
	       TableQuery.from(CustomerEntity.class)
	       .select(new String[] {"Email"});

    	// Entityresolver definieren, der die Entität auf den E-Mail-Wert projiziert.
    	EntityResolver<String> emailResolver = new EntityResolver<String>() {
            @Override
            public String resolve(String PartitionKey, String RowKey, Date timeStamp, HashMap<String, EntityProperty> properties, String etag) {
                return properties.get("Email").getValueAsString();
            }
        };

        // Schleife durch die Ergebnisse durchlaufen und die E-Mail-Informationen anzeigen.
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

## <a name="InsertOrReplace"> </a>Gewusst wie: Einfügen oder Ersetzen einer Entität

Es kommt häufig vor, dass Sie eine Entität in eine Tabelle einfügen möchten, ohne zuvor prüfen zu müssen, ob diese Entität bereits in der Tabelle vorhanden ist. Mit einem Einfügen-oder-Ersetzen-Vorgang genügt eine einzelne Anforderung, um eine nicht vorhandene Entität einzufügen oder eine vorhandene zu ersetzen. Ausgehend von vorherigen Beispielen wird durch diesen Code die Entität für "Walter Harp" eingefügt oder ersetzt. Nachdem eine neue Entität erstellt worden ist, ruft dieser Code die Methode **TableOperation.insertOrReplace** auf. Anschließend ruft der Code die Methode **execute** für das **CloudTable**-Objekt auf und spezifiziert als Parameter die Tabelle und den Einfügen-oder-Ersetzen-Vorgang. Wenn nur ein Teil einer Entität aktualisiert werden sollen, kann stattdessen die Methode **TableOperation.insertOrMerge** verwendet werden. Hinweis: Einfügen-oder-Ersetzen-Vorgänge werden auf dem lokalen Speicheremulator nicht unterstützt, weshalb dieser Code nur bei Verwendung eines Kontos auf dem Tabellendienst ausgeführt wird. Weitere Informationen zu Einfügen-oder-Ersetzen- und Einfügen-oder-Zusammenfügen-Vorgängen finden Sie in diesem [Blogbeitrag][].

    try
    {
        // Speicherkonto aus Verbindungszeichenfolge abrufen.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Tabellenclient erstellen.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Neue Kundenentität erstellen.
        CustomerEntity customer5 = new CustomerEntity("Harp", "Walter");
        customer5.setEmail("Walter@contoso.com");
        customer5.setPhoneNumber("425-555-0106");

        // Vorgang erstellen, um den neuen Kunden in die Tabelle "personen" einzufügen.
        TableOperation insertCustomer5 = TableOperation.insertOrReplace(customer5);

        // Den Vorgang an den Tabellendienst senden.
        cloudTable.execute(insertCustomer5);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="DeleteEntity"> </a>Gewusst wie: Löschen einer Entität

Sie können eine Entität problemlos nach dem Abrufen löschen. Wenn die Entität aufgerufen worden ist, rufen Sie die **TableOperation.delete**-Methode mit der zu löschenden Entität auf. Rufen Sie dann **execute** für das **CloudTable**-Objekt auf. Durch den nachstehenden Code wird eine Kundenentität aufgerufen und gelöscht.

    try
    {
        // Speicherkonto aus Verbindungszeichenfolge abrufen.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Tabellenclient erstellen.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Vorgang für den Aufruf der Entität mit Partitionsschlüssel "Smith" und Zeilenschlüssel "Jeff" erstellen.
        TableOperation retrieveSmithJeff = TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

        // Entität mit Partitionsschlüssel "Smith" und Zeilenschlüssel "Jeff" abrufen.
        CustomerEntity entitySmithJeff =
            cloudTable.execute(retrieveSmithJeff).getResultAsType();

        // Vorgang für das Löschen der Entität erstellen.
        TableOperation deleteSmithJeff = TableOperation.delete(entitySmithJeff);

        // Den Löschen-Vorgang an den Tabellendienst senden.
        cloudTable.execute(deleteSmithJeff);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="DeleteTable"> </a>Gewusst wie: Löschen einer Tabelle

Schließlich wird mit dem folgenden Code eine Tabelle aus einem Speicherkonto gelöscht. Eine gelöschte Tabelle kann für eine bestimmte Zeitdauer, in der Regel maximal vierzig Sekunden, nach dem Löschvorgang nicht neu erstellt werden.

    try
    {
        // Speicherkonto aus Verbindungszeichenfolge abrufen.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Tabellenclient erstellen.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Tabelle einschließlich aller Daten, falls vorhanden, löschen.
        CloudTable cloudTable = new CloudTable("people",tableClient);
        cloudTable.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="NextSteps"> </a>Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen der Tabellenspeicherung vertraut gemacht haben, folgen Sie diesen Links, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.

- [Azure Storage-SDK für Java]
- [Azure Storage-Client-SDK-Referenz]
- [Azure Storage-REST-API]
- [Azure Storage-Teamblog]

[Azure SDK für Java]: http://www.windowsazure.com/de-de/develop/java/
[Azure Storage SDK für Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK für Android]: https://github.com/azure/azure-storage-android
[Azure Storage-Client-SDK-Referenz]: http://dl.windowsazure.com/storage/javadoc/
[Azure Storage-REST-API]: http://msdn.microsoft.com/de-de/library/azure/gg433040.aspx
[Blog des Azure-Speicherteams]: http://blogs.msdn.com/b/windowsazurestorage/
[Blogeintrag]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
