<properties linkid="dev-java-how-to-use-table-storage" urlDisplayName="Table Service" pageTitle="How to use table storage (Java) | Microsoft Azure" metaKeywords="Azure table storage service, Azure table service Java, table storage Java" description="Learn how to use the table storage service in Azure. Code samples are written in Java code." metaCanonical="" services="storage" documentationCenter="Java" title="How to use the Table storage service from Java" authors="" solutions="" manager="" editor="" />

Verwenden des Tabellenspeicherdiensts von Java
==============================================

In diesem Leitfaden wird die Durchführung häufiger Szenarien mit dem Azure-Tabellenspeicherdienst demonstriert. Die Beispiele sind in Java-Code geschrieben. Die behandelten Szenarien umfassen das **Erstellen und Löschen einer Tabelle sowie das Einfügen und Abfragen von Tabellenentitäten**. Weitere Informationen zu Tabellen finden Sie im Abschnitt [Nächste Schritte](#NextSteps).

Inhaltsverzeichnis
------------------

-   [Was ist Table Storage](#what-is)
-   [Konzepte](#Concepts)
-   [Erstellen eines Azure-Speicherkontos](#CreateAccount)
-   [Erstellen einer Java-Anwendung](#CreateApplication)
-   [Konfigurieren Ihrer Anwendung für den Zugriff auf Table Storage](#ConfigureStorage)
-   [Einrichten einer Azure-Speicherverbindungszeichenfolge](#ConnectionString)
-   [Vorgehensweise: Erstellen einer Tabelle](#CreateTable)
-   [Vorgehensweise: Hinzufügen einer Entität zu einer Tabelle](#AddEntity)
-   [Vorgehensweise: Einfügen eines Entitätsbatchs](#InsertBatch)
-   [Vorgehensweise: Abrufen aller Entitäten in einer Partition](#RetrieveEntities)
-   [Vorgehensweise: Abrufen eines Entitätsbereichs in einer Partition](#RetrieveRange)
-   [Vorgehensweise: Abrufen einer einzelnen Entität](#RetriveSingle)
-   [Vorgehensweise: Ändern einer Entität](#ModifyEntity)
-   [Vorgehensweise: Abfragen einer Teilmenge von Entitätseigenschaften](#QueryProperties)
-   [Vorgehensweise: Einfügen-oder-Ersetzen einer Entität](#InsertOrReplace)
-   [Vorgehensweise: Löschen einer Entität](#DeleteEntity)
-   [Vorgehensweise: Löschen einer Tabelle](#DeleteTable)
-   [Nächste Schritte](#NextSteps)

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

Erstellen eines Azure-Speicherkontos
------------------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Erstellen einer Java-Anwendung
------------------------------

In diesem Leitfaden werden Sie Speicherfunktionen verwenden, die lokal in einer Java-Anwendung oder in Code ausgeführt werden können, der in einer Webrolle oder in einer Workerrolle in Azure ausgeführt wird. Es wird vorausgesetzt, dass Sie das Java Development Kit (JDK) heruntergeladen und installiert, die Anweisungen zur Installation der Azure-Bibliotheken für Java und des Azure SDK in [Azure SDK für Java](http://www.windowsazure.com/de-de/develop/java/) befolgt und in Ihrem Abonnement ein Azure-Speicherkonto angelegt haben.

Sie können die Anwendung mit beliebigen Entwicklungstools erstellen, unter anderem auch mit Notepad. Sie müssen lediglich wissen, wie Sie ein Java-Projekt kompilieren und auf die Azure-Bibliotheken für Java verweisen.

Konfigurieren Ihrer Anwendung für den Zugriff auf Table Storage
---------------------------------------------------------------

Fügen Sie folgende Import-Anweisungen am Anfang der Java-Datei dort ein, wo Azure-Speicher-APIs auf Tabellen zugreifen sollen:

    // Folgende Importanweisungen für die Verwendung von Tabellen-APIs einschließen
    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.table.client.*;
    import com.microsoft.windowsazure.services.table.client.TableQuery.*;

Einrichten einer Azure-Speicherverbindungszeichenfolge
------------------------------------------------------

Ein Azure-Speicherclient verwendet eine Speicherverbindungszeichenfolge zum Speichern von Endpunkten und Anmeldeinformationen für den Zugriff auf Datenverwaltungsdienste. Bei der Ausführung in einer Clientanwendung muss die Speicherverbindungszeichenfolge in dem unten gezeigten Format angegeben werden. Dabei müssen der Name Ihres Speicherkontos und der primäre Zugriffsschlüssel für das im Verwaltungsportal aufgeführte Speicherkonto als *AccountName-* und *AccountKey-* Werte eingegeben werden. Dieses Beispiel zeigt, wie Sie ein statisches Feld für die Verbindungszeichenfolge deklarieren:

    // Verbindungszeichenfolge mit Ihren Werten definieren
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

In einer Anwendung, die in einer Azure-Rolle ausgeführt wird, kann diese Zeichenfolge in der Dienstkonfigurationsdatei ServiceConfiguration.cscfg gespeichert werden. Der Zugriff darauf kann dann durch Aufruf der Methode **RoleEnvironment.getConfigurationSettings** erfolgen. Dieses Beispiel zeigt, wie die Verbindungszeichenfolge von einem **Setting**-Element mit der Bezeichnung *StorageConnectionString* in der Dienstkonfigurationsdatei abgerufen wird:

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

Die Beispiele unten gehen davon aus, dass Sie eine dieser beiden Definitionen verwendet haben, um die Speicherverbindungszeichenfolge abzurufen.

Vorgehensweise: Erstellen einer Tabelle
---------------------------------------

Mit einem **CloudTableClient**-Objekt können Sie Referenzobjekte für Tabellen und Entitäten abrufen. Durch den folgenden Code wird ein **CloudTableClient**-Objekt erstellt und verwendet, um eine neue Tabelle zu erstellen. Der gesamte Code in diesem Leitfaden verwendet eine in der Dienstkonfiguration der Azure-Anwendung gespeicherte Speicherverbindungszeichenfolge. Es gibt auch andere Möglichkeiten, **CloudStorageAccount**-Objekte zu erstellen.

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Tabellenclient erstellen.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Tabelle erstellen, wenn diese noch nicht vorhanden ist.
    String tableName = "personen";
    tableClient.createTableIfNotExists(tableName);

Vorgehensweise: Hinzufügen einer Entität zu einer Tabelle
---------------------------------------------------------

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

Tabellenvorgänge, die Entitäten umfassen, benötigen ein **TableOperation**-Objekt. Dieses Objekt definiert den für eine Entität vorgesehenen Vorgang, der mit einem **CloudTableClient**-Objekt ausgeführt werden kann. Durch den folgenden Code wird eine neue Instanz der **CustomerEntity**-Klasse erstellt, wobei einige Kundendaten gespeichert werden sollen. Als Nächstes ruf der Code **TableOperation.insert** auf, um ein **TableOperation**-Objekt für das Einfügen einer Entität in eine Tabelle zu erstellen, und weist dieser Entität die neue **CustomerEntity** zu. Abschließend ruft der Code die **execute**-Methode für das **CloudTableClient**-Objekt auf und gibt dabei die Tabelle "personen" und das neue **TableOperation**-Objekt an, das dann eine Anforderung an den Speicherdienst sendet, die neue Kundenentität in die Tabelle "personen" einzufügen.

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Tabellenclient erstellen.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Neue Kundenentität erstellen.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.setEmail("Walter@contoso.com");
    customer1.setPhoneNumber("425-555-0101");

    // Vorgang erstellen, um den neuen Kunden in die Tabelle "personen" einzufügen.


    // Den Vorgang an den Tabellendienst senden.
    tableClient.execute("personen", insertCustomer1);

Vorgehensweise: Einfügen eines Entitätsbatchs
---------------------------------------------

Sie können einen Entitätsbatch in einem Schreibvorgang in den Tabellendienst einfügen. Durch den folgenden Code wird ein **TableBatchOperation**-Objekt erstellt, zu dem anschließend drei Einfügevorgänge hinzugefügt werden. Für jeden Einfügevorgang wird ein neues Entitätsobjekt erstellt, dessen Werte werden festgelegt, und dann wird die Methode **insert** für das **TableBatchOperation**-Objekt aufgerufen und die Entität einem neuen Einfügevorgang zugeordnet. Anschließend ruft der Code die **execute**-Methode für das **CloudTableClient**-Objekt auf und gibt dabei die Tabelle "personen" und das **TableBatchOperation**-Objekt an, das dann den Batch mit Tabellenvorgängen in einer einzelnen Anforderung an den Speicherdienst sendet. Beachten Sie im Zusammenhang mit Batchvorgängen Folgendes:

1.  Bis zu 100 Einfüge-, Lösch-, Zusammenführungs-, Ersetzungs-, Einfüge- oder Zusammenführungs- und Einfüge- oder Ersetzungsvorgänge können in beliebiger Kombination in einem einzelnen Batch ausgeführt werden.
2.  Ein Batchvorgang kann einen Abfragevorgang enthalten, allerdings nur als einzigen Vorgang in diesem Batch.
3.  Alle Entitäten in einem Batchvorgang müssen über denselben Partitionsschlüssel verfügen.
4.  Ein Batchvorgang ist auf eine Nutzlast von 4 MB Daten beschränkt.

<!-- -->

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Tabellenclient erstellen.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Batchvorgang definieren.
    TableBatchOperation batchOperation = new TableBatchOperation();

    // Kundenentität erstellen und zur Tabelle hinzufügen.
    CustomerEntity customer = new CustomerEntity("Smith", "Jeff");
    customer.setEmail("Jeff@contoso.com");
    customer.setPhoneNumber("425-555-0104");
    batchOperation.insert(customer);

    // Zweite Kundenentität erstellen und zur Tabelle hinzufügen.
    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.setEmail("Ben@contoso.com");
    customer2.setPhoneNumber("425-555-0102");
    batchOperation.insert(customer2);

    // Dritte Kundenentität erstellen und zur Tabelle hinzufügen.
    CustomerEntity customer3 = new CustomerEntity("Smith", "Denise");
    customer3.setEmail("Denise@contoso.com");
    customer3.setPhoneNumber("425-555-0103");
    batchOperation.insert(customer3);

    // Vorgangsbatch für Tabelle "personen" ausführen.
    tableClient.execute("personen", batchOperation);

Vorgehensweise: Abrufen aller Entitäten in einer Partition
----------------------------------------------------------

Verwenden Sie ein **TableQuery**-Objekt, um eine Tabelle für Entitäten in einer Partition abzurufen. Rufen Sie **TableQuery.from** auf, um eine Abfrage in einer bestimmten Tabelle zu erstellen, die einen bestimmen Ergebnistyp übergibt. Im folgenden Code wird ein Filter für Entitäten erstellt, wobei "Smith" der Partitionsschlüssel ist. **TableQuery.generateFilterCondition** ist eine Helper-Methode für die Erstellung von Filtern für Abfragen. Rufen Sie **where** auf der von der **TableQuery.from**-Methode übergebenen Referenz auf, um den Filter auf die Abfrage anzuwenden. Wenn die Abfrage mit einem Aufruf der **execute**-Methode für das **CloudTableClient**-Objekt ausgeführt wird, übergibt sie einen **Iterator** mit **CustomerEntity** als Ergebnistyp. Anschließend können Sie den übergebenen **Iterator** verwenden, um die Ergebnisse in jede Schleife einzubinden. In diesem Code werden die Felder der einzelnen Entitäten in den Abfrageergebnissen an die Konsole ausgegeben.

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Tabellenclient erstellen.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Filterbedingung mit dem Partitionsschlüssel "Smith" erstellen.
    String partitionFilter = TableQuery.generateFilterCondition(
        TableConstants.PARTITION_KEY, 
        QueryComparisons.EQUAL,
        "Smith");

    // Partitionsabfrage mit "Smith" als Partitionsschlüsselfilter erstellen.
    TableQuery partitionQuery =
        TableQuery.from("personen", CustomerEntity.class)
        .where(partitionFilter);

    // Schleife durch die Ergebnisse durchlaufen und Informationen zur Entität anzeigen.
    for (CustomerEntity entity : tableClient.execute(partitionQuery)) {
        System.out.println(entity.getPartitionKey() + " " + entity.getRowKey() + 
            "\t" + entity.getEmail() + "\t" + entity.getPhoneNumber());
    }

Vorgehensweise: Abrufen eines Entitätsbereichs in einer Partition
-----------------------------------------------------------------

Wenn Sie nicht alle Entitäten in einer Partition abrufen möchten, können Sie durch Verwendung von Vergleichsoperatoren einen Bereich angeben. Im folgenden Code werden zwei Filter kombiniert, um alle Entitäten in der Partition "Smith" abzurufen, deren Zeilenschlüssel (Vorname) mit einem Buchstaben vor dem Buchstaben "E" im Alphabet beginnen. Danach werden die Abfrageergebnisse ausgegeben. Wenn Sie die Entitäten verwenden, die in diesem Leitfaden im Kapitel zum Batcheinfügevorgang in die Tabelle eingefügt wurden, werden in diesem Fall nur zwei Entitäten übergeben (Ben und Denise Smith); Jeff Smith ist nicht enthalten.

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Tabellenclient erstellen.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Filterbedingung mit dem Partitionsschlüssel "Smith" erstellen.
    String partitionFilter = TableQuery.generateFilterCondition(
        TableConstants.PARTITION_KEY, 
        QueryComparisons.EQUAL,
        "Smith");

    // Filterbedingung mit einem Zeilenschlüssel erstellen, der kleiner ist als die Anzahl der Buchstaben bis zum Buchstaben "E".
    String rowFilter = TableQuery.generateFilterCondition(
        TableConstants.ROW_KEY, 
        QueryComparisons.LESS_THAN,
        "E");

    // Die beiden Bedingungen in einem Filterausdruck kombinieren.
    String combinedFilter = TableQuery.combineFilters(partitionFilter, 
            Operators.AND, rowFilter);

    // Bereichsabfrage mit "Smith" als Partitionsschlüssel erstellen.
    // wobei der Zeilenschlüssel nur bis zum Buchstaben "E" geht.
    TableQuery rangeQuery =
        TableQuery.from("personen", CustomerEntity.class)
        .where(combinedFilter);

    // Schleife durch die Ergebnisse durchlaufen und Informationen zur Entität anzeigen.
    for (CustomerEntity entity : tableClient.execute(rangeQuery)) {
        System.out.println(entity.getPartitionKey() + " " + entity.getRowKey() + 
            "\t" + entity.getEmail() + "\t" + entity.getPhoneNumber());
    }

Vorgehensweise: Abrufen einer einzelnen Entität
-----------------------------------------------

Sie können eine Abfrage schreiben, um eine bestimmte Entität abzurufen. Im folgenden Code wird der Kunde "Jeff Smith" nicht durch Erstellen einer **TableQuery** und Verwendung von Filtern übergeben, sondern durch Aufruf von **TableOperation.retrieve** mit Partitions- und Zeilenschlüsselparametern. Bei der Ausführung übergibt dieser Abfragevorgang nicht eine ganze Sammlung von Entitäten, sondern nur eine einzelne Entität. Die Methode **getResultAsType** wandelt das Ergebnis in den Typ des Zuweisungsziels um, ein **CustomerEntity**-Objekt. Wenn dieser Typ mit dem für die Abfrage spezifizierten Typ nicht kompatibel ist, wird eine Ausnahme ausgelöst. Ein Nullwert wird übergeben, wenn keine Entität eine übereinstimmende Partition und einen passenden Zeilenschlüssel hat. Die Angabe beider Schlüssel, Partition und Zeile, in einer Abfrage ist die schnellste Möglichkeit, um eine einzelne Entität aus dem Tabellendienst abzurufen.

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Tabellenclient erstellen.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Entität mit Partitionsschlüssel "Smith" und Zeilenschlüssel "Jeff" abrufen
    TableOperation retrieveSmithJeff = 
        TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Den Vorgang an den Tabellendienst senden und die betreffende Entität abrufen.
    CustomerEntity specificEntity =
        tableClient.execute("personen", retrieveSmithJeff).getResultAsType();

Vorgehensweise: Ändern einer Entität
------------------------------------

Um eine Entität zu ändern, rufen Sie sie aus dem Tabellendienst ab, ändern Sie das Entitätsobjekt, und speichern Sie die Änderungen dann mit einem Ersetzungs- oder Zusammenführungsvorgang im Tabellendienst. Mit dem folgenden Code wird die Telefonnummer eines vorhandenen Kunden geändert. In diesem Fall wird nicht wie beim Einfügen **TableOperation.insert**, sondern **TableOperation.replace** aufgerufen. Die Methode **CloudTableClient.execute** ruft den Tabellendienst auf, und die Entität wird ersetzt, sofern sie nicht nach dem Aufruf durch diese Anwendung in der Zwischenzeit von einer anderen Anwendung geändert wurde. In diesem Fall wird eine Ausnahme ausgelöst, und die Entität muss erneut aufgerufen, geändert und gespeichert werden. Dieses auf dem "Optimistic Concurrency"-Verfahren (optimistisches Locking) basierende Muster für Wiederholungsversuche ist in verteilten Speichersystemen häufig anzutreffen.

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Tabellenclient erstellen.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Entität mit Partitionsschlüssel "Smith" und Zeilenschlüssel "Jeff" abrufen.
    TableOperation retrieveSmithJeff = 
        TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Den Vorgang an den Tabellendienst senden und die betreffende Entität abrufen.
    CustomerEntity specificEntity =
        tableClient.execute("personen", retrieveSmithJeff).getResultAsType();
        
    // Neue Telefonnummer angeben.
    specificEntity.setPhoneNumber("425-555-0105");

    // Vorgang für das Ersetzen der Entität erstellen.
    TableOperation replaceEntity = TableOperation.replace(specificEntity);

    // Den Vorgang an den Tabellendienst senden.
    tableClient.execute("personen", replaceEntity);

Vorgehensweise: Teilmenge von Entitätseigenschaften abfragen
------------------------------------------------------------

Mit einer Abfrage einer Tabelle können nur einige wenige Eigenschaften einer Entität aufgerufen werden. Bei dieser Methode, der sogenannten Projektion, wird die Bandbreite reduziert und die Abfrageleistung gesteigert, vor allem bei großen Entitäten. Die Abfrage im folgenden Code übergibt mit der Methode **select** nur die E-Mail-Adressen von Entitäten in der Tabelle. Die Ergebnisse werden mit Unterstützung eines **EntityResolver**, der die Typumwandlung der vom Server übergebenen Entitäten übernimmt, in eine Sammlung von **String**-Objekten projiziert. Weitere Informationen zur Projektion finden Sie in diesem [Blogbeitrag](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx). Beachten Sie, dass die Projektion nicht auf dem lokalen Speicheremulator unterstützt wird und dieser Code deshalb nur bei der Verwendung eines Kontos für den Tabellendienst ausgeführt wird.

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Tabellenclient erstellen.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Projektionsabfrage definieren, die nur die E-Mail-Eigenschaft abruft
    TableQuery projectionQuery = 
        TableQuery.from("personen", CustomerEntity.class)
        .select(new String[] {"Email"});

    // Entityresolver definieren, der die Entität auf den E-Mail-Wert projiziert.
    EntityResolver emailResolver = new EntityResolver() {
        @Override
        public String resolve(String PartitionKey, String RowKey, Date timeStamp,
                HashMap properties, String etag) {
            return properties.get("Email").getValueAsString();
        }
        }

    // Schleife durch die Ergebnisse durchlaufen und die E-Mail-Informationen anzeigen.
    for (String projectedString : 
            tableClient.execute(projectionQuery, emailResolver)) {
        System.out.println(projectedString);
    }

Vorgehensweise: Einfügen-oder-Ersetzen einer Entität
----------------------------------------------------

Es kommt häufig vor, dass Sie eine Entität in eine Tabelle einfügen möchten, ohne zuvor prüfen zu müssen, ob diese Entität bereits in der Tabelle vorhanden ist. Mit einem Einfügen-oder-Ersetzen-Vorgang genügt eine einzelne Anforderung, um eine nicht vorhandene Entität einzufügen oder eine vorhandene zu ersetzen. Ausgehend von vorherigen Beispielen wird durch diesen Code die Entität für "Walter Harp" eingefügt oder ersetzt. Nachdem eine neue Entität erstellt worden ist, ruft dieser Code die Methode **TableOperation.insertOrReplace** auf. Anschließend ruft der die Methode **execute** für das **CloudTableClient**-Objekt auf und spezifiziert als Parameter die Tabelle und den Einfügen-oder-Ersetzen-Vorgang. Wenn nur ein Teil einer Entität aktualisiert werden sollen, kann stattdessen die Methode **TableOperation.insertOrMerge** verwendet werden. Hinweis: Einfügen-oder-Ersetzen-Vorgänge werden auf dem lokalen Speicheremulator nicht unterstützt, weshalb dieser Code nur bei Verwendung eines Kontos auf dem Tabellendienst ausgeführt wird. Weitere Informationen zu Einfügen-oder-Ersetzen- und Einfügen-oder-Zusammenfügen-Vorgängen finden Sie in diesem [Blogbeitrag](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx).

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Tabellenclient erstellen.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Neue Kundenentität erstellen.
    CustomerEntity customer5 = new CustomerEntity("Harp", "Walter");
    customer5.setEmail("Walter@contoso.com");
    customer5.setPhoneNumber("425-555-0106");

    // Vorgang erstellen, um den neuen Kunden in die Tabelle "personen" einzufügen.
    TableOperation insertCustomer5 = TableOperation.insertOrReplace(customer5);

    // Den Vorgang an den Tabellendienst senden.
    tableClient.execute("personen", insertCustomer5);

Vorgehensweise: Löschen einer Entität
-------------------------------------

Sie können eine Entität problemlos nach dem Abrufen löschen. Wenn die Entität aufgerufen worden ist, rufen Sie die **TableOperation.delete**-Methode mit der zu löschenden Entität auf. Rufen Sie dann **execute** für das **CloudTableClient**-Objekt auf. Durch den nachstehenden Code wird eine Kundenentität aufgerufen und gelöscht.

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Tabellenclient erstellen.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Vorgang für den Aufruf der Entität mit Partitionsschlüssel "Smith" und Zeilenschlüssel "Jeff" erstellen.
    TableOperation retrieveSmithJeff = TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Entität mit Partitionsschlüssel "Smith" und Zeilenschlüssel "Jeff" abrufen.
    CustomerEntity entitySmithJeff =
            tableClient.execute("personen", retrieveSmithJeff).getResultAsType();

    // Vorgang für das Löschen der Entität erstellen.
    TableOperation deleteSmithJeff = TableOperation.delete(entitySmithJeff);

    // Den Löschen-Vorgang an den Tabellendienst senden.
    tableClient.execute("personen", deleteSmithJeff);

Vorgehensweise: Löschen einer Tabelle
-------------------------------------

Schließlich wird mit dem folgenden Code eine Tabelle aus einem Speicherkonto gelöscht. Eine gelöschte Tabelle kann für eine bestimmte Zeitdauer, in der Regel maximal vierzig Sekunden, nach dem Löschvorgang nicht neu erstellt werden.

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Tabellenclient erstellen.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Tabelle einschließlich aller Daten, falls vorhanden, löschen.
    tableClient.deleteTableIfExists("personen");

Nächste Schritte
----------------

Nachdem Sie sich nun mit den Grundlagen der Tabellenspeicherung vertraut gemacht haben, folgen Sie diesen Links, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Speichern und Zugreifen auf Daten in Azure]
-   Besuchen Sie den [Blog des Azure Storage-Teams](http://blogs.msdn.com/b/windowsazurestorage/)

