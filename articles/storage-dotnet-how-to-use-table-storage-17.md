<properties linkid="dev-net-2-how-to-table-services" urlDisplayName="Table Service (2.0)" pageTitle="How to use table storage | Microsoft Azure" metaKeywords="Get started Azure table, Azure nosql, Azure large structured data store, Azure table, Azure table storage, Azure table .NET, Azure table storage .NET, Azure table C#, Azure table storage C#" description="Learn how to use table storage to create and delete tables and insert and query entities in a table." metaCanonical="" services="storage" documentationCenter=".NET" title="How to use the Table Storage Service" authors="" solutions="" manager="paulettm" editor="cgronlun" />

Verwenden des Tabellenspeicherdienstes
======================================

[Version 1.7](/en-us/develop/net/how-to-guides/table-services-v17/ "Version 1.7") [Version 2.0](/en-us/develop/net/how-to-guides/table-services/ "Version 2.0")

Dieser Artikel beschreibt gängige Szenarien für die Verwendung des Tabellenspeicherdienstes in Azure. Die Beispiele sind in C\#-Code geschrieben und verwenden die .NET API. Behandelt werden unter anderem die Szenarien **Erstellen und Löschen von Tabellen, Einfügen und Abfragen von Entitäten in Tabellen**. Weitere Informationen zu Tabellen finden Sie im Abschnitt [Nächste Schritte](#next-steps).

Inhaltsverzeichnis
------------------

-   [Was ist der Tabellenspeicherdienst?](#what-is)
-   [Konzepte](#concepts)
-   [Erstellen eines Azure-Speicherkontos](#create-account)
-   [Einrichten einer Verbindungszeichenfolge für den Speicher](#setup-connection-string)
-   [Vorgehensweise: Programmgesteuerter Zugriff auf den Tabellenspeicher](#configure-access)
-   [Vorgehensweise: Erstellen einer Tabelle](#create-table)
-   [Vorgehensweise: Hinzufügen einer Entität zu einer Tabelle](#add-entity)
-   [Vorgehensweise: Einfügen eines Entitätsbatchs](#insert-batch)
-   [Vorgehensweise: Abrufen aller Entitäten einer Partition](#retrieve-all-entities)
-   [Vorgehensweise: Abrufen eines Bereichs von Entitäten in einer Partition](#retrieve-range-entities)
-   [Vorgehensweise: Abrufen einer einzelnen Entität](#retrieve-single-entity)
-   [Vorgehensweise: Aktualisieren einer Entität](#update-entity)
-   [Vorgehensweise: Abfragen einer Teilmenge von Entitäteneigenschaften](#query-entity-properties)
-   [Vorgehensweise: Einfügen oder Ersetzen einer Entität](#insert-entity)
-   [Vorgehensweise: Löschen einer Entität](#delete-entity)
-   [Vorgehensweise: Löschen einer Tabelle](#delete-table)
-   [Nächste Schritte](#next-steps)

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

Erstellen eines KontosErstellen eines Azure-Speicherkontos
----------------------------------------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Einrichten einer VerbindungszeichenfolgeEinrichten einer Verbindungszeichenfolge für den Speicher
-------------------------------------------------------------------------------------------------

Die Azure .NET Speicher-API unterstützt Speicher-Verbindungszeichenfolgen zum Konfigurieren von Endpunkten und Anmeldeinformationen für den Zugriff auf Speicherdienste. Sie können Ihre Speicher-Verbindungszeichenfolge in einer Konfigurationsdatei angeben, anstatt sie hart zu codieren:

-   Bei der Verwendung von Azure-Cloud-Diensten sollten Sie Ihre Verbindungszeichenfolge mithilfe des Azure-Dienstkonfigurationssystems speichern (`*.csdef`- und `*.cscfg`-Dateien).
-   Bei der Verwendung von Azure-Websites oder virtuellen Computern in Azure sollten Sie Ihre Verbindungszeichenfolge mithilfe des .NET-Konfigurationssystems speichern (`web.config`-Datei).

In beiden Fällen können Sie Ihre Verbindungszeichenfolge über die `CloudConfigurationManager.GetSetting`-Methode abrufen, wie später in dieser Anleitung beschrieben.

### Konfigurieren Ihrer Verbindungszeichenfolge für Cloud-Dienste

Azure-Cloud-Dienst-Projekte bieten einen einzigartigen Dienstkonfigurationsmechanismus, mit dem Sie Ihre Konfigurationseinstellungen im Azure-Verwaltungsportal dynamisch ändern können, ohne Ihre Anwendung neu bereitstellen zu müssen.

So konfigurieren Sie die Verbindungszeichenfolge in der Azure-Dienstkonfiguration:

1.  Klicken Sie im Projektmappen-Explorer von Visual Studio im Ordner **Roles** des Azure-Bereitstellungsprojekts mit der rechten Maustaste auf Ihre Webrolle oder Workerrolle, und klicken Sie dann auf **Eigenschaften**.
     ![Blob5](./media/storage-dotnet-how-to-use-table-storage-17/blob5.png)

2.  Klicken Sie auf die Registerkarte **Einstellungen** und anschließend auf die Schaltfläche **Einstellung hinzufügen**.
     ![Blob6](./media/storage-dotnet-how-to-use-table-storage-17/blob6.png)

    In der Einstellungstabelle wird daraufhin der neue Eintrag **Setting1** angezeigt.

3.  Wählen Sie in der Dropdownliste **Typ** des neuen Eintrags **Setting1** die Option **Verbindungszeichenfolge** aus.
     ![Blob7](./media/storage-dotnet-how-to-use-table-storage-17/blob7.png)

4.  Klicken Sie auf die Schaltfläche **...** rechts neben dem Eintrag **Setting1**. Daraufhin öffnet sich der Dialogfeld **Speicherkonto-Verbindungszeichenfolge**.

5.  Wählen Sie aus ob Sie den Speicheremulator (eine Simulation des Azure-Speichers auf Ihrem lokalen Computer) oder ein tatsächliches Speicherkonto in der Cloud als Ziel verwenden möchten. Der Code in diesem Leitfaden kann mit beiden Optionen verwendet werden. Geben Sie den Wert für **Primärer Zugriffsschlüssel** ein, den Sie an früherer Stelle in diesem Lernprogramm kopiert haben, wenn Sie Blob-Daten in dem Speicherkonto speichern möchten, das zuvor in Azure erstellt wurde.
     ![Blob8](./media/storage-dotnet-how-to-use-table-storage-17/blob8.png)

6.  Ändern Sie den **Namen** von **Setting1** zu einem "lesbareren" Namen wie z. B. **StorageConnectionString**. Sie werden später in diesem Leitfaden auf diese Verbindungszeichenfolge verweisen.
     ![Blob9](./media/storage-dotnet-how-to-use-table-storage-17/blob9.png)

### Konfigurieren der Verbindungszeichenfolge bei Verwendung von Websites oder virtuellen Computern

Bei der Verwendung von Websites oder virtuellen Computern sollten Sie das .NET-Konfigurationssystem verwenden (`web.config`). Sie speichern die Verbindungszeichenfolge mit dem Element `<appSettings>`:

    <configuration>
        <appSettings>
            <add key="StorageConnectionString"
                 value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
        </appSettings>
    </configuration>

Weitere Informationen zu Speicherverbindungszeichenfolgen finden Sie unter [Konfigurieren von Windows Azure-Verbindungszeichenfolgen](http://msdn.microsoft.com/en-us/library/windowsazure/ee758697.aspx).

Sie sind nun bereit, um die einzelnen Aufgaben in dieser Anleitung auszuführen.

Programmgesteuerter ZugriffVorgehensweise: Programmgesteuerter Zugriff auf den Tabellenspeicher
-----------------------------------------------------------------------------------------------

Fügen Sie die folgenden Namespace-Deklarationen zum Anfang aller C\#-Dateien hinzu, in denen Sie programmgesteuert auf den Azure-Speicher zugreifen möchten:

    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.StorageClient;

Sie können Ihre Speicherverbindungszeichenfolge und Speicherkontoinformationen mit dem Typ **CloudStorageAccount** und dem Typ **CloudConfigurationManager** aus der Azure-Dienstkonfiguration abrufen:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Erstellen einer TabelleVorgehensweise: Erstellen einer Tabelle
--------------------------------------------------------------

Das **CloudTableClient**-Objekt bietet Zugriff auf Referenzobjekte für Tabellen und Entitäten. Der folgende Code erstellt ein **MobileServiceClient**-Objekt und verwendet dieses Objekt für die Erstellung einer neuen Tabelle. Der gesamte Code in dieser Anleitung verwendet eine Speicher-Verbindungszeichenfolge, die in der Dienstkonfiguration der Azure-Anwendung gespeichert ist. Es gibt auch andere Möglichkeiten zum Erstellen von **CloudStorageAccount**-Objekten.

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Tabellenclient erstellen
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Tabelle erstellen, wenn diese noch nicht vorhanden ist
    string tableName = "people";
    tableClient.CreateTableIfNotExist(tableName);

Hinzufügen einer Entität zu einer TabelleVorgehensweise: Hinzufügen einer Entität zu einer Tabelle
--------------------------------------------------------------------------------------------------

Entitäten werden mithilfe einer aus **TableServiceEntity** abgeleiteten benutzerdefinierten Klasse C\#-Objekten zugeordnet. Erstellen Sie zunächst eine Klasse, mit der die Eigenschaften der Entität definiert werden, um eine Entität zu einer Tabelle hinzuzufügen. Mit dem folgenden Code wird eine Entitätsklasse definiert, die den Vornamen des Kunden als Zeilenschlüssel und den Nachnamen als Partitionsschlüssel verwendet. Durch den Partitions- und den Zeilenschlüssel einer Entität wird die Entität in der Tabelle eindeutig identifiziert. Entitäten mit demselben Partitionsschlüssel können schneller abgefragt werden als solche mit unterschiedlichem Partitionsschlüssel.

    public class CustomerEntity : TableServiceEntity
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

Tabellenoperationen mit Entitäten benötigen ein **TableServiceContext**-Objekt. Dieses Objekt enthält den clientseitigen Zustand aller Tabellenentitäten, die im Clientcode erstellt und abgerufen werden. Durch das Vorhalten des clientseitigen Objekts werden Schreibvorgänge effizienter, da beim Ausführen von Speicherungsvorgängen nur Objekte mit Änderungen im Tabellenspeicherdienst aktualisiert werden. Der folgende Code erstellt ein **TableServiceContext**-Objekt durch einen Aufruf der **GetDataServiceContext**-Methode. Anschließend erstellt der Code eine Instanz der **CustomerEntity**-Klasse. Der Code ruft **serviceContext.AddObject** auf, um die neue Entität in die Tabelle einzufügen. Dabei wird das Entitätsobjekt zum **serviceContext** hinzugefügt, allerdings erfolgt noch keine Dienstoperation. Zuletzt schickt der Code die neue Entität an den Tabellenspeicherdienst, indem er die **SaveChangesWithRetries**-Methode aufruft.

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Tabellenclient erstellen
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Datendienstkontext abrufen
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Neue Kundenentität erstellen
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Neuen Kunden zur Personentabelle hinzufügen
    serviceContext.AddObject("people", customer1);

    // Operation an den Tabellenspeicherdienst übermitteln
    serviceContext.SaveChangesWithRetries();

Einfügen eines EntitätsbatchsVorgehensweise: Einfügen eines Entitätsbatchs
--------------------------------------------------------------------------

Der folgende Code fügt einen Batch von Entitäten in einem Schreibvorgang in den Tabellenspeicherdienst ein. Der folgende Code erstellt drei Entitätsobjekte und fügt diese mit der **AddObject**-Methode zum Dienstkontext hinzu. Anschließend wird **SaveChangesWithRetries** mit dem **SaveChangesOptions.Batch**-Parameter aufgerufen. Wenn Sie **SaveChangesOptions.Batch** auslassen, werden drei separate Aufrufe an den Tabellenspeicherdienst ausgeführt. Beachten Sie im Zusammenhang mit Batchvorgängen Folgendes:

1.  Sie können die Batchfunktion zum Aktualisieren, Löschen und Einfügen verwenden.
2.  Ein einzelner Batchvorgang kann bis zu 100 Entitäten umfassen.
3.  Alle Entitäten in einem Batchvorgang müssen über denselben Partitionsschlüssel verfügen.

<!-- -->

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Tabellenclient erstellen
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    string tableName = "people";

    // Datendienstkontext abrufen
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Kundenentität erstellen und zur Tabelle hinzufügen
    CustomerEntity customer = new CustomerEntity("Smith", "Jeff");
    customer.Email = "Jeff@contoso.com";
    customer.PhoneNumber = "425-555-0104";
    serviceContext.AddObject(tableName, customer);

    // Weitere Kundenentität erstellen und zur Tabelle hinzufügen
    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    customer2.PhoneNumber = "425-555-0102";
    serviceContext.AddObject(tableName, customer2);

    // Kundenentität erstellen und zur Tabelle hinzufügen
    CustomerEntity customer3 = new CustomerEntity("Smith", "Denise");
    customer3.Email = "Denise@contoso.com";
    customer3.PhoneNumber = "425-555-0103";
    serviceContext.AddObject(tableName, customer3);

    // Operation an den Tabellenspeicherdienst übermitteln
    serviceContext.SaveChangesWithRetries(SaveChangesOptions.Batch);

Abrufen aller EntitätenVorgehensweise: Abrufen aller Entitäten einer Partition
------------------------------------------------------------------------------

Verwenden Sie eine LINQ-Anfrage, um alle Entitäten in einer Partition aus einer Tabelle abzurufen. Rufen Sie **serviceContext.CreateQuery** auf, um eine Abfrage in Ihrer Datenquelle zu erstellen. Im folgenden Code wird ein Filter für Entitäten erstellt, wobei Smith der Partitionsschlüssel ist. Rufen Sie **AsTableServiceQuery&lt;CustomerEntity\>** auf das Ergebnis der LINQ-Abfrage auf, um die Erstellung des **CloudTableQuery**-Objekts abzuschließen. Anschließend können Sie das erstellte **partitionQuery**-Objekt in einer **foreach**-Schleife verwenden, um mit den Ergebnissen zu arbeiten. Dieser Code gibt die Felder der einzelnen Entitäten in den Abfrageergebnissen an die Konsole aus.

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Tabellenclient erstellen
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Datendienstkontext abrufen
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Angabe einer Partitionsabfrage mit "Smith" als Partitionsschlüssel
    CloudTableQuery<CustomerEntity> partitionQuery =
        (from e in serviceContext.CreateQuery<CustomerEntity>("people")
         where e.PartitionKey == "Smith"
         select e).AsTableServiceQuery<CustomerEntity>();

    // Durchlaufen der Ergebnisse und Anzeigen von Informationen über die Entität
    foreach (CustomerEntity entity in partitionQuery)
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

Abrufen eines EntitätsbereichsVorgehensweise: Abrufen eines Bereichs von Entitäten in einer Partition
-----------------------------------------------------------------------------------------------------

Wenn Sie nicht alle Entitäten in einer Partition abrufen möchten, können Sie einen Bereich angeben, indem Sie die **CompareTo**-Methode anstelle der gängigen größer/kleiner als-Operatoren (&lt; \>) verwenden. Letztere Methode führt dazu, dass die Abfrage nicht korrekt aufgebaut wird. Im folgenden Codebeispiel werden zwei Filter eingesetzt, um alle Entitäten in der Partition Smith abzurufen, deren Zeilenschlüssel (Vorname) mit einem Buchstaben vor dem Buchstaben E im Alphabet beginnen. Danach werden die Abfrageergebnisse ausgegeben. Wenn Sie die im Bereich Batch-Einfügungen dieser Anleitung erstellten Entitäten verwenden, gibt diese Abfrage nur zwei Entitäten zurück (Ben und Denise Smith); Jeff Smith wird nicht zurückgegeben.

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Tabellenclient erstellen
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Datendienstkontext abrufen
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Angabe einer Partitionsabfrage mit "Smith" als Partitionsschlüssel
    // und Zeilenschlüssel bis zum Buchstaben "E"
    CloudTableQuery<CustomerEntity> entityRangeQuery =
        (from e in serviceContext.CreateQuery<CustomerEntity>("people")
         where e.PartitionKey == "Smith" && e.RowKey.CompareTo("E") < 0
         select e).AsTableServiceQuery<CustomerEntity>();

    // Durchlaufen der Ergebnisse und Anzeigen von Informationen über die Entität
    foreach (CustomerEntity entity in entityRangeQuery)
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

Abrufen einer einzelnen EntitätVorgehensweise: Abrufen einer einzelnen Entität
------------------------------------------------------------------------------

Sie können eine Abfrage schreiben, um eine einzelne bestimmte Entität abzurufen. Im folgenden Code wird der Kunde Jeff Smith mithilfe von zwei Filtern abgerufen. Anstelle von **AsTableServiceQuery** wird in diesem Code **FirstOrDefault** aufgerufen. Diese Methode gibt anstelle einer Sammlung nur eine Entität zurück, und der Code weist den Rückgabewert direkt an ein **CustomerEntity**-Objekt zu. Wenn keine Entität mit exakter Partitions- und Zeilenübereinstimmung existiert, wird ein Nullwert zurückgegeben. Die Angabe eines Partitions- und Zeilenschlüssels in einer Abfrage ist die schnellste Möglichkeit, um eine einzelne Entität aus dem Tabellendienst abzurufen.

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Tabellenclient erstellen
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Datendienstkontext abrufen
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Entität mit Partitionsschlüssel "Smith" und Zeilenschlüssel "Jeff" zurückgeben
    CustomerEntity specificEntity =
        (from e in serviceContext.CreateQuery<CustomerEntity>("people")
         where e.PartitionKey == "Smith" && e.RowKey == "Jeff"
         select e).FirstOrDefault();

Aktualisieren einer EntitätVorgehensweise: Aktualisieren einer Entität
----------------------------------------------------------------------

Um eine Entität zu aktualisieren, rufen Sie sie aus dem Tabellendienst ab, ändern Sie das Entitätsobjekt, und speichern Sie die Änderungen dann im Tabellendienst. Mit dem folgenden Code wird die Telefonnummer eines vorhandenen Kunden geändert. Anstelle von **AddObject** wie beim Einfügen wird in diesem Code **UpdateObject** aufgerufen. Die **SaveChangesWithRetries**-Methode ruft den Tabellenspeicherdienst auf. Die Entität wird aktualisiert, sofern diese nicht seit dem Abrufen von einer anderen Anwendung geändert wurde. In diesem Fall wird eine Ausnahme geworfen, und die Entität muss erneut abgerufen, geändert und gespeichert werden. Dieses Wiederholungsmuster ist typisch für verteilte Speichersysteme.

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Tabellenclient erstellen
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Datendienstkontext abrufen
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Entität mit Partitionsschlüssel "Smith" und Zeilenschlüssel "Jeff" zurückgeben
    CustomerEntity specificEntity =
        (from e in serviceContext.CreateQuery<CustomerEntity>("people")
         where e.PartitionKey == "Smith" && e.RowKey == "Jeff"
         select e).FirstOrDefault();

    // Neue Telefonnummer angeben
    specificEntity.PhoneNumber = "425-555-0105";

    // Entität aktualisieren
    serviceContext.UpdateObject(specificEntity);

    // Operation an den Tabellenspeicherdienst übermitteln
    serviceContext.SaveChangesWithRetries();

Abfragen einer Teilmenge von EigenschaftenVorgehensweise: Abfragen einer Teilmenge von Entitäteneigenschaften
-------------------------------------------------------------------------------------------------------------

Mit einer Tabellenabfrage können nur einige wenige Eigenschaften von einer Entität abgefragt werden. Mit dieser Technik, der sogenannten Projektion, wird die Bandbreite reduziert und die Abfrageleistung gesteigert, vor allem bei großen Entitäten. Mit der Abfrage im folgenden Code werden nur die E-Mail-Adressen von Entitäten in der Tabelle zurückgegeben. Weitere Informationen zur Projektion finden Sie in diesem [Blogeintrag](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx). Beachten Sie, dass die Projektion nicht auf dem lokalen Speicheremulator unterstützt wird und dieser Code deshalb nur bei der Verwendung eines Kontos für den Tabellendienst ausgeführt wird.

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Tabellenclient erstellen
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Datendienstkontext abrufen
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Projektionsabfrage definieren, die nur die E-Mail-Eigenschaft abruft
    var projectionQuery = 
        from e in serviceContext.CreateQuery<CustomerEntity>("people")
        select new
        {
            Email = e.Email
            // Hier können Sie weitere Felder angeben
        };

    // Durchlaufen der Ergebnisse und Anzeigen des E-Mail-Werts
    foreach (var person in projectionQuery)
    {
        Console.WriteLine(person.Email);
    }

Einfügen oder Ersetzen einer EntitätVorgehensweise: Einfügen oder Ersetzen einer Entität
----------------------------------------------------------------------------------------

Manchmal möchten Sie eine Entität in eine Tabelle einfügen, ohne zu wissen, ob diese dort bereits existiert. Mit einer insert-or-replace-Operation können Sie eine einzelne Abfrage erstellen, um die Entität einzufügen, falls diese noch nicht existiert und andernfalls zu aktualisieren. Auf der Basis der vorherigen Beispiele fügt der folgende Code die Entität für Walter Harp ein bzw. aktualisiert diese. Nach dem Erstellen der neuen Entität ruft der Code die **serviceContext.AttachTo**-Methode auf. Anschließend ruft der Code **UpdateObject** und zuletzt **SaveChangesWithRetries** mit dem **SaveChangesOptions.ReplaceOnUpdate**-Parameter auf. Wenn Sie den **SaveChangesOptions.ReplaceOnUpdate**-Parameter auslassen, wird eine insert-or-merge-Operation ausgeführt. Beachten Sie, dass insert-or-replace nicht im lokalen Speicheremulator unterstützt wird und dieser Code deshalb nur bei der Verwendung eines Kontos für den Tabellendienst ausgeführt wird. Weitere Informationen zu insert-or-replace und insert-or-merge finden Sie in diesem [Blogeintrag](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx).

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Tabellenclient erstellen
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Datendienstkontext abrufen
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Neue Kundenentität erstellen
    CustomerEntity customer5 = new CustomerEntity("Harp", "Walter");
    customer5.Email = "Walter@contoso.com";
    customer5.PhoneNumber = "425-555-0106";

    // Kunden an die Personentabelle anhängen
    serviceContext.AttachTo("people", customer5);

    // Kunden einfügen falls neu, ansonsten ersetzen
    serviceContext.UpdateObject(customer5);

    // Übermitteln der Operation an den Tabellenspeicherdienst unter Verwendung der ReplaceOnUpdate-Option
    serviceContext.SaveChangesWithRetries(SaveChangesOptions.ReplaceOnUpdate);

Löschen einer EntitätVorgehensweise: Löschen einer Entität
----------------------------------------------------------

Sie können eine Entität problemlos nach dem Abrufen löschen. Alternativ können Sie die Entität mit der **AttachTo**-Methode überwachen, ohne diese vom Server abzurufen (siehe insert-or-replace weiter oben). Sobald die Entität mit **serviceContext** überwacht wird, können Sie **DeleteObject** mit der Entität aufrufen, um diese zu löschen. Rufen Sie anschließend **SaveChangesWithRetries** auf. Mit dem folgenden Code wird eine Kundenentität abgerufen und gelöscht.

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Tabellenclient erstellen
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Datendienstkontext abrufen
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    CustomerEntity specificEntity =
        (from e in serviceContext.CreateQuery<CustomerEntity>("people")
         where e.PartitionKey == "Smith" && e.RowKey == "Jeff"
         select e).FirstOrDefault();

    // Entität löschen
    serviceContext.DeleteObject(specificEntity);

    // Operation an den Tabellenspeicherdienst übermitteln
    serviceContext.SaveChangesWithRetries();

Löschen einer TabelleVorgehensweise: Löschen einer Tabelle
----------------------------------------------------------

Zuletzt wird mit dem folgenden Code eine Tabelle aus einem Speicherkonto gelöscht. Eine gelöschte Tabelle kann für eine bestimmte Zeitdauer nach dem Löschvorgang nicht neu erstellt werden.

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Tabellenclient erstellen
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Tabelle löschen, falls diese existiert
    tableClient.DeleteTableIfExist("people");

Nächste SchritteNächste Schritte
--------------------------------

Nachdem Sie sich nun mit den Grundlagen von Tabellenspeicher vertraut gemacht haben, folgen Sie diesen Links, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.

-   Vollständige Informationen zu verfügbaren APIs finden Sie in der Blobdienst-Referenzdokumentation:
    -   [Referenz zur .NET-Clientbibliothek](http://msdn.microsoft.com/en-us/library/windowsazure/wl_svchosting_mref_reference_home)
    -   [REST-API-Referenz](http://msdn.microsoft.com/en-us/library/windowsazure/dd179355)
-   Weitere Informationen zu fortgeschrittenen Aufgaben mit Azure-Speicher finden Sie unter [Speichern und Zugreifen auf Daten in Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx).
-   Weitere Informationen zu zusätzlichen Optionen für das Speichern von Daten in Azure finden Sie in den anderen Featureleitfäden.
    -   Verwenden Sie [Blobspeicher](/en-us/develop/net/how-to-guides/blob-storage/) zum Speichern unstrukturierter Daten.
    -   Verwenden Sie eine [SQL-Datenbank](/en-us/develop/net/how-to-guides/sql-database/) zum Speichern relationaler Daten.

