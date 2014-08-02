<properties  linkid="dev-net-2-how-to-table-services" urlDisplayName="Table Service" pageTitle="How to use table storage  from .NET | Microsoft Azure" metaKeywords="Get started Azure table   Azure nosql   Azure large structured data store   Azure table   Azure table storage   Azure table .NET   Azure table storage .NET   Azure table C#   Azure table storage C#" description="Learn how to use table storage to create and delete tables and insert and query entities in a table." services="storage" documentationCenter=".NET" metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="How to use the Table Storage Service" authors="tamram" />

# Verwenden des Tabellenspeicherdiensts von .NET

In diesem Leitfaden wird die Durchführung häufiger Szenarien mit dem Azure-Tabellenspeicherdienst demonstriert. Die Beispiele sind in C# geschrieben und greifen auf die Azure-Speicherclientbibliothek für .NET zurück. Die behandelten Szenarien umfassen das **Erstellen und Löschen einer Tabelle** sowie das **Arbeiten mit Tabellenentitäten**. Weitere Informationen zu Tabellen finden Sie im Abschnitt [Nächste Schritte](#next-steps).

## Inhaltsverzeichnis

* [Was ist der Tabellendienst?](#what-is)
* [Konzepte](#concepts)
* [Erstellen eines Azure-Speicherkontos](#create-account)
* [Einrichten einer
  Speicherverbindungszeichenfolge](#setup-connection-string)
* [Gewusst wie: Programmgesteuertes Zugreifen auf
  Tabellenspeicher](#configure-access)
* [Gewusst wie: Erstellen einer Tabelle](#create-table)
* [Gewusst wie: Hinzufügen einer Entität zu einer Tabelle](#add-entity)
* [Gewusst wie: Einfügen eines Entitätsbatchs](#insert-batch)
* [Gewusst wie: Abrufen aller Entitäten in einer
  Partition](#retrieve-all-entities)
* [Gewusst wie: Abrufen eines Entitätsbereichs in einer
  Partition](#retrieve-range-entities)
* [Gewusst wie: Abrufen einer einzelnen
  Entität](#retrieve-single-entity)
* [Gewusst wie: Ersetzen einer Entität](#replace-entity)
* [Gewusst wie: Einfügen oder Ersetzen einer
  Entität](#insert-or-replace-entity)
* [Gewusst wie: Abfragen einer Teilmenge von
  Entitätseigenschaften](#query-entity-properties)
* [Gewusst wie: Löschen einer Entität](#delete-entity)
* [Gewusst wie: Löschen einer Tabelle](#delete-table)
* [Nächste Schritte](#next-steps)

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

<h2><a name="create-account"></a><span  class="short-header">Erstellen eines Kontos</span>Erstellen eines Azure-Speicherkontos</h2>


[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

<h2><a name="setup-connection-string"></a><span  class="short-header">Einrichten einer Verbindungszeichenfolge</span>Einrichten einer Speicherverbindungszeichenfolge</h2>


Die Azure-Speicherclientbibliothek für .NET unterstützt die Verwendung einer Speicherverbindungszeichenfolge zum Konfigurieren von Endpunkten und Anmeldeinformationen für den Zugriff auf Speicherdienste. Sie können die Speicherverbindungszeichenfolge in einer Konfigurationsdatei ablegen, statt sie fest in Code zu programmieren:

* Bei der Verwendung von Azure-Clouddiensten empfiehlt es sich, die
  Verbindungszeichenfolge mithilfe des Azure-Dienstkonfigurationssystems
  (Dateien `*.csdef` und `*.cscfg`) zu speichern.
* Bei der Verwendung von Azure-Websites und virtuellen Azure-Computern
  oder der Erstellung von .NET-Anwendungen, die nicht in Azure
  ausgeführt werden sollen, empfiehlt es sich, die
  Verbindungszeichenfolge mit dem .NET-Konfigurationssystem (z. B. der
  Datei `web.config` oder `app.config`) zu speichern.

In beiden Fällen können Sie die Verbindungszeichenfolge mit der
`CloudConfigurationManager.GetSetting`-Methode abrufen, wie später in
diesem Leitfaden gezeigt.

### Konfigurieren der Verbindungszeichenfolge bei der Verwendung von Clouddiensten

Die Dienstkonfiguration ist in Azure-Clouddienstprojekten eindeutig und ermöglicht es Ihnen, Konfigurationseinstellungen im Azure-Verwaltungsportal dynamisch zu ändern, ohne die Anwendung erneut bereitzustellen.

So konfigurieren Sie die Verbindungszeichenfolge in der Azure-Dienstkonfiguration:

1.  Klicken Sie im Projektmappen-Explorer von Visual Studio im Ordner
    **Rollen** des Azure-Bereitstellungsprojekts mit der rechten
    Maustaste auf Ihre Webrolle oder Workerrolle, und klicken Sie dann
    auf **Eigenschaften**.  
     ![Blob5](./media/storage-dotnet-how-to-use-table-storage-20/blob5.png)

2.  Klicken Sie auf die Registerkarte **Einstellungen**, und wählen Sie
    die Schaltfläche **Einstellung hinzufügen** aus.  
     ![Blob6](./media/storage-dotnet-how-to-use-table-storage-20/blob6.png)
    
    In der Einstellungstabelle wird daraufhin der neue Eintrag
    **Setting1** angezeigt.

3.  Wählen Sie in der Dropdownliste **Typ** des neuen Eintrags
    **Setting1** die Option **Verbindungszeichenfolge** aus.  
     ![Blob7](./media/storage-dotnet-how-to-use-table-storage-20/blob7.png)

4.  Klicken Sie auf die Schaltfläche **...** rechts neben dem Eintrag
    **Setting1**. Das Dialogfeld **Verbindungszeichenfolge für das
    Speicherkonto** wird geöffnet.

5.  Wählen Sie aus, ob das Ziel der Speicheremulator (simulierter
    Azure-Speicher auf dem lokalen Computer) oder ein Speicherkonto in
    der Cloud sein soll. Der Code in diesem Leitfaden kann mit beiden
    Optionen verwendet werden. Geben Sie den Wert **Primärer
    Zugriffsschlüssel** ein, den Sie an früherer Stelle in diesem
    Lernprogramm kopiert haben, wenn Sie Blobdaten in dem Speicherkonto
    speichern möchten, das zuvor in Azure erstellt wurde.   
     ![Blob8](./media/storage-dotnet-how-to-use-table-storage-20/blob8.png)

6.  Ändern Sie den Eintrag **Name** von **Setting1** in einen
    benutzerfreundlicheren Namen wie **StorageConnectionString**. Sie
    verweisen später im Code in diesem Leitfaden auf diese
    Verbindungszeichenfolge.  
     ![Blob9](./media/storage-dotnet-how-to-use-table-storage-20/blob9.png)

### Konfigurieren der Verbindungszeichenfolge in der .NET-Konfiguration

Wenn Sie eine Anwendung schreiben, bei der es sich nicht um einen Azure-Clouddienst handelt (siehe vorheriger Abschnitt), empfiehlt es sich, das .NET-Konfigurationssystem (z. B. `web.config` oder `app.config`) zu verwenden. Dies umfasst Azure-Websites oder virtuelle Azure-Computer sowie Anwendungen, die für die Ausführung außerhalb von Azure konzipiert sind. Verwenden Sie folgenden Code, um die Verbindungszeichenfolge mit dem Element `<a ppSettings>` zu speichern:

    <configuration>
       <a ppSettings>    
			<a dd  key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=
			[Kontoname];AccountKey=[Kontoschlüssel]" />
       <a ppSettings>  
	</configuration>

 Weitere Informationen zu Speicherverbindungszeichenfolgen finden Sie
unter [Konfigurieren von Windows Azure-Verbindungszeichenfolgen][1].

 Sie sind nun bereit für die Gewusst-wie-Aufgaben in diesem Leitfaden.

 <h2> <a name="configure-access"> </a><span  class="short-header">Programmgesteuerter Zugriff</span>Gewusst wie: Programmgesteuertes Zugreifen auf Tabellenspeicher</h2>


 <h3>Abrufen der Assembly</h3>


 Mithilfe von NuGet können Sie die
`Microsoft.WindowsAzure.Storage.dll`-Assembly abrufen. Klicken Sie im
**Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt,
und wählen Sie **Manage NuGet Packages** aus. Suchen Sie online nach
"WindowsAzure.Storage", und klicken Sie auf **Installieren**, um das
Azure-Speicherpaket und die zugehörigen Abhängigkeiten zu installieren.

 `Microsoft.WindowsAzure.Storage.dll` ist auch im Azure SDK für .NET 2.0
enthalten, das aus dem [.NET Developer Center][2] heruntergeladen werden kann. Die Assembly wird im Verzeichnis `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk>\ref\` installiert.

 <h3>Namespace-Deklarationen</h3>


 Fügen Sie zu Beginn aller C#-Dateien, in denen Sie programmgesteuert auf
Azure-Speicher zugreifen möchten, die folgenden Namespace-Codedeklarationen hinzu:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;

 Stellen Sie sicher, dass auf die
`Microsoft.WindowsAzure.Storage.dll`-Assembly verwiesen wird.

 <h3>Abrufen der Verbindungszeichenfolge</h3>


 Sie können Ihre Speicherkontoinformationen mit dem Typ
**CloudStorageAccount** darstellen. Wenn Sie eine Azure-Projektvorlage
verwenden und/oder über einen Verweis auf den Namespace
"Microsoft.WindowsAzure.CloudConfigurationManager" verfügen, können Sie
Ihre Speicherverbindungszeichenfolge und Speicherkontoinformationen mit dem Typ **CloudConfigurationManager** aus der Azure-Dienstkonfiguration abrufen:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

 Wenn Sie eine Anwendung ohne einen Verweis auf
"Microsoft.WindowsAzure.CloudConfigurationManager" erstellen und die
Verbindungszeichenfolge sich in `web.config` oder `app.config` befindet, wie oben gezeigt, können Sie die Verbindungszeichenfolge mit
**ConfigurationManager** abrufen. Sie müssen einen Verweis auf
"System.Configuration.dll" zu Ihrem Projekt und eine andere
Namespace-Deklaration dafür hinzufügen:

    using System.Configuration;
    ...
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

 <h3>ODataLib-Abhängigkeiten</h3>


 ODataLib-Abhängigkeiten in der Speicherclientbibliothek für .NET werden
durch die ODataLib-Pakete (Version 5.0.2), die über NuGet verfügbar sind, und nicht durch WCF Data Services aufgelöst. Die ODataLib-Bibliotheken können direkt heruntergeladen werden, oder es wird
über Ihr Codeprojekt durch NuGet darauf verwiesen. Die spezifischen
ODataLib-Pakete sind [OData][3], [Edm][4] und [Spatial][5].

 <h2><a name="create-table"></a><span  class="short-header">Erstellen einer Tabelle</span>Gewusst wie: Erstellen einer Tabelle</h2>


 Mit einem **CloudTableClient**-Objekt können Sie Referenzobjekte für
Tabellen und Entitäten abrufen. Durch den folgenden Code wird ein
**CloudTableClient**-Objekt erstellt und verwendet, um eine neue Tabelle
zu erstellen. Bei allem Code in diesem Leitfaden wird davon ausgegangen, dass es sich bei der erstellten Anwendung um ein Azure-Clouddienstprojekt handelt und eine in der Dienstkonfiguration der Azure-Anwendung gespeicherte Speicherverbindungszeichenfolge verwendet wird.

    // Speicherkonto aus Verbindungszeichenfolge abrufen.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Tabellenclient erstellen.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    
    // Tabelle erstellen, wenn diese noch nicht vorhanden ist.
    CloudTable table = tableClient.GetTableReference("people");
    table.CreateIfNotExists();

 <h2><a name="add-entity"></a><span  class="short-header">Hinzufügen einer Entität zu einer Tabelle</span>Gewusst wie: Hinzufügen einer Entität zu einer Tabelle</h2>


 Entitäten werden mithilfe einer aus **TableEntity** abgeleiteten
benutzerdefinierten Klasse C#-Objekten zugeordnet. Erstellen Sie eine Klasse, mit der die Eigenschaften der Entität definiert werden, um eine Entität zu einer Tabelle hinzuzufügen. Mit dem folgenden Code wird eine Entitätsklasse definiert, die den Vornamen des Kunden als Zeilenschlüssel und den Nachnamen als Partitionsschlüssel verwendet. In Kombination miteinander wird mit dem Partitions- und Zeilenschlüssel eine Entität in der Tabelle eindeutig identifiziert. Entitäten mit demselben Partitionsschlüssel können schneller abgerufen werden als Entitäten mit unterschiedlichen Partitionsschlüsseln, die Verwendung verschiedener Partitionsschlüssel ermöglicht jedoch eine größere parallele Vorgangsskalierbarkeit. Bei jeder Eigenschaft, die im Tabellendienst gespeichert werden soll, muss es sich um eine öffentliche Eigenschaft eines unterstützten Typs handeln, die sowohl `get` als auch
`set` verfügbar macht. Darüber hinaus *muss* der Entitätstyp einen
parameterlosen Konstruktor verfügbar machen.

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

 Tabellenvorgänge, die Entitäten umfassen, werden mit dem
**CloudTable**-Objekt durchgeführt, das Sie unter "Gewusst wie:
Erstellen einer Tabelle" erstellt haben. Der durchzuführende Vorgang wird durch ein **TableOperation**-Objekt dargestellt. Im folgenden Codebeispiel wird die Erstellung des **CloudTable**-Objekts und danach eines **CustomerEntity**-Objekts gezeigt. Um den Vorgang vorzubereiten, wird eine **TableOperation** zum Einfügen der Kundenentität in die Tabelle erstellt. Schließlich wird der Vorgang durch den Aufruf von
**CloudTable.Execute** ausgeführt.

    // Speicherkonto aus Verbindungszeichenfolge abrufen.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Tabellenclient erstellen.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    
    // CloudTable-Objekt erstellen, das die Tabelle "people" darstellt.
    CloudTable table = tableClient.GetTableReference("people");
    
    // Neue Kundenentität erstellen.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";
    
    // TableOperation erstellen, mit der die Kundenentität eingefügt wird.
    TableOperation insertOperation = TableOperation.Insert(customer1);
    
    // Einfügevorgang ausführen.
    table.Execute(insertOperation);

 <h2><a name="insert-batch"></a><span  class="short-header">Einfügen eines Entitätsbatchs</span>Gewusst wie: Einfügen eines Entitätsbatchs</h2>


 Sie können einen Entitätsbatch in einem Schreibvorgang in eine Tabelle
einfügen. Beachten Sie im Zusammenhang mit Batchvorgängen Folgendes:

1.  Sie können Aktualisierungs-, Lösch- und Einfügevorgänge in einem
    einzigen Batchvorgang ausführen.
2.  Ein einzelner Batchvorgang kann bis zu 100 Entitäten umfassen. 3.  Alle Entitäten in einem Batchvorgang müssen über denselben
    Partitionsschlüssel verfügen.
4.  Eine Abfrage kann als Batchvorgang durchgeführt werden, dabei muss
    es sich jedoch um den einzigen Vorgang im Batch handeln.

 <!-- -->
 Im folgenden Codebeispiel werden zwei Entitätsobjekte erstellt und mit
der **Insert**-Methode jeweils einer **TableBatchOperation** hinzugefügt. Danach wird **CloudTable.Execute** aufgerufen, um den Vorgang auszuführen.

		// Speicherkonto aus Verbindungszeichenfolge abrufen.
		CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
		// Tabellenclient erstellen.
		CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    
		// CloudTable-Objekt erstellen, das die Tabelle "people" darstellt.
		CloudTable table = tableClient.GetTableReference("people");
    
		// Batchvorgang erstellen.
		TableBatchOperation batchOperation = new TableBatchOperation();
    
		// Kundenentität erstellen und zur Tabelle hinzufügen.
		CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
		customer1.Email = "Jeff@contoso.com";
		customer1.PhoneNumber = "425-555-0104";
		
		// Weitere Kundenentität erstellen und zur Tabelle hinzufügen.
		CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
		customer2.Email = "Ben@contoso.com";
		customer2.PhoneNumber = "425-555-0102";
		
		// Beide Kundenentitäten zum Batcheinfügevorgang hinzufügen.
		batchOperation.Insert(customer1);
		    batchOperation.Insert(customer2);
		 
		// Batchvorgang ausführen.
		table.ExecuteBatch(batchOperation);

 <h2><a name="retrieve-all-entities"></a><span  class="short-header">Abrufen aller Entitäten</span>Gewusst wie: Abrufen aller Entitäten in einer Partition</h2>


 Verwenden Sie ein **TableQuery**-Objekt, um eine Tabelle für alle
Entitäten in einer Partition abzurufen. Im folgenden Codebeispiel wird ein Filter für Entitäten erstellt, wobei "Smith" der Partitionsschlüssel ist. In diesem Beispiel werden die Felder der einzelnen Entitäten in den Abfrageergebnissen an die Konsole ausgegeben.

	    // Speicherkonto aus Verbindungszeichenfolge abrufen.
	    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
	        CloudConfigurationManager.GetSetting("StorageConnectionString"));
	    
	    // Tabellenclient erstellen.
	    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
	    
	    // CloudTable-Objekt erstellen, das die Tabelle "people" darstellt.
	    CloudTable table = tableClient.GetTableReference("people");
	    
	    // Abfragevorgang für alle Kundenentitäten erstellen, wobei gilt: PartitionKey="Smith".
	    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));
	    
	    // Felder für die einzelnen Kunden ausgeben.
	    foreach (CustomerEntity entity in table.ExecuteQuery(query))
	    {
	        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
	            entity.Email, entity.PhoneNumber);
	    }

 <h2><a name="retrieve-range-entities"></a><span  class="short-header">Abrufen eines Entitätsbereichs</span>Gewusst wie: Abrufen eines Entitätsbereichs in einer Partition</h2>


 Wenn Sie nicht alle Entitäten in einer Partition abrufen möchten, können
Sie einen Bereich angeben, indem Sie den Partitionsschlüsselfilter mit einem Zeilenschlüsselfilter kombinieren. Im folgenden Codebeispiel werden zwei Filter eingesetzt, um alle Entitäten in der Partition
"Smith" abzurufen, deren Zeilenschlüssel (Vorname) mit einem Buchstaben
vor dem Buchstaben E im Alphabet beginnen. Danach werden die Abfrageergebnisse gedruckt.

	    // Speicherkonto aus Verbindungszeichenfolge abrufen.
	    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
	        CloudConfigurationManager.GetSetting("StorageConnectionString"));
	    
	    // Tabellenclient erstellen.
	    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
	    
	    // CloudTable-Objekt erstellen, das die Tabelle "people" darstellt.
	    CloudTable table = tableClient.GetTableReference("people");
	    
	    // Tabellenabfrage erstellen.
	    TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
	        TableQuery.CombineFilters(
	            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
	            TableOperators.And,
	            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "E")));
	    
	    // Schleife durch die Ergebnisse durchlaufen und Informationen zur Entität anzeigen.
	    foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
	    {
	        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
	            entity.Email, entity.PhoneNumber);
	    }

 <h2><a name="retrieve-single-entity"></a><span  class="short-header">Abrufen einer einzelnen Entität</span>Gewusst wie: Abrufen einer einzelnen Entität</h2>


 Sie können eine Abfrage schreiben, um eine einzelne bestimmte Entität
abzurufen. Im folgenden Code wird der Kunde "Ben Smith" durch eine
**TableOperation** angegeben. Bei dieser Methode wird nur eine Entität
anstelle einer Sammlung zurückgegeben, und bei dem zurückgegebenen Wert in **TableResult.Result** handelt es sich um eine **CustomerEntity**. Die Angabe eines Partitions- und Zeilenschlüssels in einer Abfrage ist die schnellste Möglichkeit, um eine einzelne Entität aus dem Tabellendienst abzurufen.

	    // Speicherkonto aus Verbindungszeichenfolge abrufen.
	    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
	        CloudConfigurationManager.GetSetting("StorageConnectionString"));
	    
	    // Tabellenclient erstellen.
	    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
	    
	    // CloudTable-Objekt erstellen, das die Tabelle "people" darstellt.
	    CloudTable table = tableClient.GetTableReference("people");
	    
	    // Abfragevorgang erstellen, der eine Kundenentität übernimmt.
	    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
	    
	    // Abfragevorgang ausführen.
	    TableResult retrievedResult = table.Execute(retrieveOperation);
	    
	    // Telefonnummer des Ergebnisses drucken.
	    if (retrievedResult.Result != null)
	       Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
	    else
	       Console.WriteLine("The phone number could not be retrieved.");

 <h2><a name="replace-entity"></a><span  class="short-header">Ersetzen einer Entität</span>Gewusst wie: Ersetzen einer Entität</h2>


 Um eine Entität zu aktualisieren, rufen Sie sie aus dem Tabellendienst
ab, ändern Sie das Entitätsobjekt, und speichern Sie die Änderungen dann im Tabellendienst. Mit dem folgenden Code wird die Telefonnummer eines vorhandenen Kunden geändert. Anstelle von **Insert** wird in diesem Code
**Replace** aufgerufen. Dadurch wird die Entität auf dem Server
vollständig ersetzt, sofern die Entität auf dem Server sich seit dem letzten Abruf nicht geändert hat. In diesem Fall wäre der Vorgang nicht erfolgreich. Auf diese Weise soll verhindert werden, dass eine Änderung, die zwischen dem Abruf und der Aktualisierung durch eine andere Komponente der Anwendung vorgenommen wurde, unabsichtlich überschrieben wird. Die richtige Vorgehensweise in diesem Fall besteht darin, die Entität erneut abzurufen, Ihre Änderungen vorzunehmen (falls diese noch gültig sind) und dann einen anderen **Replace**-Vorgang auszuführen. Im nächsten Abschnitt erfahren Sie, wie Sie dieses Verhalten überschreiben.

	    // Speicherkonto aus Verbindungszeichenfolge abrufen.
	    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
	        CloudConfigurationManager.GetSetting("StorageConnectionString"));
	    
	    // Tabellenclient erstellen.
	    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
	    
	    // CloudTable-Objekt erstellen, das die Tabelle "people" darstellt.
	    CloudTable table = tableClient.GetTableReference("people");
	    
	    // Abfragevorgang erstellen, der eine Kundenentität übernimmt.
	    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
	    
	    // Vorgang ausführen.
	    TableResult retrievedResult = table.Execute(retrieveOperation);
	    
	    // Ergebnis einem CustomerEntity-Objekt zuweisen.
	    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;
	    
	    if (updateEntity != null)
	    {
	       // Telefonnummer ändern.
	       updateEntity.PhoneNumber = "425-555-0105";
	    
	       // InsertOrReplace TableOperation erstellen.
	       TableOperation updateOperation = TableOperation.Replace(updateEntity);
	    
	       // Vorgang ausführen.
	       table.Execute(updateOperation);
	    
	       Console.WriteLine("Entity updated.");
	    }
	    
	    else
	       Console.WriteLine("Entity could not be retrieved.");

 <h2><a name="insert-or-replace-entity"></a><span  class="short-header">Einfügen oder Ersetzen einer Entität</span>Gewusst wie: Einfügen oder Ersetzen einer Entität</h2>


<b>Ersetzungsvorgänge</b> sind nicht erfolgreich, wenn die Entität seit dem
letzten Abruf vom Server geändert wurde. Darüber hinaus müssen Sie zuerst die Entität vom Server abrufen, damit der <b>Ersetzungsvorgang</b> erfolgreich ist. Manchmal ist jedoch nicht bekannt, ob die Entität auf dem Server vorhanden ist, und die darin aktuell gespeicherten Werte sind nicht relevant. In diesem Fall sollten diese durch die Aktualisierung vollständig überschrieben werden. Dazu würden Sie einen
**InsertOrReplace**-Vorgang verwenden. Bei diesem Vorgang wird die
Entität eingefügt, falls sie nicht vorhanden ist, oder ersetzt, falls sie vorhanden ist, unabhängig davon, wann die letzte Aktualisierung stattgefunden hat. Im folgenden Codebeispiel wird die Kundenentität für Ben Smith abgerufen, sie wird danach jedoch mithilfe von
**InsertOrReplace** wieder auf dem Server gespeichert. Alle Änderungen,
die zwischen dem Abruf- und Aktualisierungsvorgang an der Entität vorgenommen wurden, werden überschrieben.

    // Speicherkonto aus Verbindungszeichenfolge abrufen.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Tabellenclient erstellen.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    
    // CloudTable-Objekt erstellen, das die Tabelle "people" darstellt.
    CloudTable table = tableClient.GetTableReference("people");
    
    // Abfragevorgang erstellen, der eine Kundenentität übernimmt.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
    
    // Vorgang ausführen.
    TableResult retrievedResult = table.Execute(retrieveOperation);
    
    // Ergebnis einem CustomerEntity-Objekt zuweisen.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;
    
    if (updateEntity != null)
    {
       // Telefonnummer ändern.
       updateEntity.PhoneNumber = "425-555-1234";
    
       // InsertOrReplace TableOperation erstellen.
       TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(updateEntity);
    
       // Vorgang ausführen.
       table.Execute(insertOrReplaceOperation);
    
       Console.WriteLine("Entity was updated.");
    }
    
    else
       Console.WriteLine("Entity could not be retrieved.");

 <h2><a name="query-entity-properties"></a><span  class="short-header">Abfragen einer Teilmenge von Eigenschaften</span>Gewusst wie: Abfragen einer Teilmenge von Entitätseigenschaften</h2>


 Mit einer Tabellenabfrage können nicht nur alle, sondern auch nur einige
Eigenschaften aus einer Entität abgerufen werden. Bei dieser Methode, der sogenannten Projektion, wird die Bandbreite reduziert und die Abfrageleistung gesteigert, vor allem bei großen Entitäten. Mit der Abfrage im folgenden Code werden nur die E-Mail-Adressen von Entitäten in der Tabelle zurückgegeben. Dies geschieht durch die Verwendung einer Abfrage von **DynamicTableEntity** und einem **EntityResolver**. Weitere Informationen zur Projektion finden Sie in diesem [Blogbeitrag][6]. Beachten Sie, dass die Projektion nicht auf dem lokalen Speicheremulator unterstützt wird und dieser Code deshalb nur bei der Verwendung eines Kontos für den Tabellendienst ausgeführt wird.

    // Speicherkonto aus Verbindungszeichenfolge abrufen.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Tabellenclient erstellen.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    
    // CloudTable-Objekt erstellen, das die Tabelle "people" darstellt.
    CloudTable table = tableClient.GetTableReference("people");
    
    // Abfrage definieren und nur die E-Mail-Eigenschaft auswählen.
    TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });
    
    // Entitätsresolver definieren, der nach dem Abruf mit der Entität verwendet wird.
    EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;
    
    foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
    {
        Console.WriteLine(projectedEmail);
    }

 <h2><a name="delete-entity"></a><span  class="short-header">Löschen einer Entität</span>Gewusst wie: Löschen einer Entität</h2>


 Sie können eine Entität problemlos nach dem Abrufen löschen. Verwenden
Sie dazu dasselbe Muster wie für das Aktualisieren einer Entität. Mit dem folgenden Code wird eine Kundenentität abgerufen und gelöscht.

    // Speicherkonto aus Verbindungszeichenfolge abrufen.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Tabellenclient erstellen.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    
    // CloudTable-Objekt erstellen, das die Tabelle "people" darstellt.
    CloudTable table = tableClient.GetTableReference("people");
    
    // Abfragevorgang erstellen, der eine Kundenentität erwartet.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
    
    // Vorgang ausführen.
    TableResult retrievedResult = table.Execute(retrieveOperation);
    
    // Ergebnis einem CustomerEntity-Objekt zuweisen.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;
    
    // TableOperation "Delete" erstellen.
    if (deleteEntity != null)
    {
       TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
    
       // Vorgang ausführen.
       table.Execute(deleteOperation);
    
       Console.WriteLine("Entity deleted.");
    }
    
    else
       Console.WriteLine("Could not retrieve the entity.");

 <h2><a name="delete-table"></a><span  class="short-header">Löschen einer Tabelle</span>Gewusst wie: Löschen einer Tabelle</h2>


 Schließlich wird mit dem folgenden Codebeispiel eine Tabelle aus einem
Speicherkonto gelöscht. Eine gelöschte Tabelle kann für eine bestimmte Zeitdauer nach dem Löschvorgang nicht neu erstellt werden.

    // Speicherkonto aus Verbindungszeichenfolge abrufen.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Tabellenclient erstellen.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    
    // CloudTable-Objekt erstellen, das die Tabelle "people" darstellt.
    CloudTable table = tableClient.GetTableReference("people");
    
    // Tabelle löschen, falls vorhanden.
    table.DeleteIfExists();

 <h2><a name="next-steps"></a><span  class="short-header">Nächste Schritte</span>Nächste Schritte</h2>


 Nachdem Sie sich nun mit den Grundlagen von Tabellenspeicher vertraut
gemacht haben, folgen Sie diesen Links, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.

 *  Vollständige Informationen zu verfügbaren APIs finden Sie in der
  Blobdienst-Referenzdokumentation:
  *  [Referenz zur Speicherclientbibliothek für .NET][7]
  *  [REST-API-Referenz][8]
  

*  Weitere Informationen zu fortgeschrittenen Aufgaben mit Azure Storage
  finden Sie unter [Speicher][9].
*  Weitere Informationen zu zusätzlichen Optionen für das Speichern von
  Daten in Azure finden Sie in den anderen Featureleitfäden.
  *  Verwenden Sie
    [Blobspeicher](/en-us/develop/net/how-to-guides/blob-storage/) zum Speichern unstrukturierter Daten.
  *  Verwenden Sie eine
    [SQL-Datenbank](/en-us/develop/net/how-to-guides/sql-database/) zum Speichern relationaler Daten.
  


 </appsettings>



[1]: http://msdn.microsoft.com/en-us/library/windowsazure/ee758697.aspx
[2]: http://www.windowsazure.com/en-us/develop/net/#
[3]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
[4]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
[5]: http://nuget.org/packages/System.Spatial/5.0.2
[6]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
[7]: http://msdn.microsoft.com/en-us/library/windowsazure/dn495001(v=azure.10).aspx
[8]: http://msdn.microsoft.com/en-us/library/windowsazure/dd179355
[9]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx