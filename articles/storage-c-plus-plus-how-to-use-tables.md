<properties 
    pageTitle="Verwenden des Tabellenspeichers (C++) | Microsoft Azure" 
    description="Erfahren Sie, wie Sie den Tabellenspeicherdienst in Azure verwenden. Die Beispiele sind in C++ geschrieben." 
    services="storage" 
    documentationCenter=".net" 
    authors="tamram" 
    manager="adinah" 
    editor=""/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/06/2015" 
    ms.author="tamram"/>

# Verwenden des Tabellenspeichers mit C++

[AZURE.INCLUDE [storage-selector-table-include](../includes/storage-selector-table-include.md)]

## Übersicht  
In diesem Leitfaden wird die Durchführung häufiger Szenarien mit dem Azure-Tabellenspeicherdienst demonstriert. Die Beispiele sind in C++ geschrieben und greifen auf die [Azure-Speicherclientbibliothek für C++](https://github.com/Azure/azure-storage-cpp/blob/v0.5.0-preview/README.md) zurück. Die behandelten Szenarien umfassen das **Erstellen und Löschen einer Tabelle** sowie das **Arbeiten mit Tabellenentitäten**.

>[AZURE.NOTE]Diese Anleitung gilt für die Azure-Speicherclientbibliothek für C++ in der Version 0.5.0 und höher. Die empfohlene Version ist Speicherclientbibliothek 0.5.0, die über [NuGet](http://www.nuget.org/packages/wastorage) oder [GitHub](https://github.com/) verfügbar ist.

[AZURE.INCLUDE [storage-table-concepts-include](../includes/storage-table-concepts-include.md)]
[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]


## Erstellen einer C++-Anwendung  
In diesem Handbuch werden Sie Speicherfunktionen verwenden, die innerhalb einer C++-Anwendung ausgeführt werden können. Dafür müssen Sie die Azure-Speicherclientbibliothek für C++ installieren und ein Azure-Speicherkonto in Ihrem Azure-Abonnement erstellen.

Zum Installieren der Azure-Speicherclientbibliothek für C++ können Sie die folgenden Methoden verwenden:

-	**Linux:** Befolgen Sie die Anweisungen auf der Seite [Azure Storage Client Library for C++ README](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) (in englischer Sprache).  
-	**Windows:** Klicken Sie in Visual Studio auf **Extras > NuGet-Paket-Manager > Paket-Manager-Konsole**. Geben Sie im Fenster der [NuGet-Paket-Manager-Konsole](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) den folgenden Befehl ein, und drücken Sie die **EINGABETASTE**:  

		Install-Package wastorage -Pre  

## Konfigurieren Ihrer Anwendung für den Zugriff auf Table Storage  
Fügen Sie folgende include-Anweisungen am Anfang der C++-Datei dort ein, wo Azure-Speicher-APIs auf Tabellen zugreifen sollen:

	#include "was/storage_account.h"  
	#include "was/table.h"

## Einrichten einer Azure-Speicherverbindungszeichenfolge  
Ein Azure-Speicherclient verwendet eine Speicherverbindungszeichenfolge zum Speichern von Endpunkten und Anmeldeinformationen für den Zugriff auf Datenverwaltungsdienste. Bei der Ausführung in einer Clientanwendung muss die Speicherverbindungszeichenfolge in dem unten dargestellten Format angegeben werden. Dabei müssen der Name Ihres Speicherkontos und der primäre Zugriffsschlüssel für das im Verwaltungsportal aufgeführte Speicherkonto als *AccountName*- und *AccountKey*-Werte eingegeben werden. Weitere Informationen über Speicherkonten und Zugriffsschlüssel finden Sie unter [Informationen zu Azure-Speicherkonten](storage-create-storage-account.md). Dieses Beispiel zeigt, wie Sie ein statisches Feld für die Verbindungszeichenfolge deklarieren:

	// Define the connection-string with your values.
	const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));

Zum Testen der Anwendung auf Ihrem lokalen Windows-Computer können Sie den [Microsoft Azure-Speicheremulator](https://msdn.microsoft.com/library/azure/hh403989.aspx) verwenden, der mit dem [Azure SDK](http://azure.microsoft.com/downloads/) installiert wird. Der Speicheremulator ist ein Dienstprogramm, das die in Azure verfügbaren Blob-, Warteschlangen- und Tabellen-Dienste auf dem lokalen Entwicklungscomputer simuliert. Im folgenden Beispiel wird dargestellt, wie Sie ein statisches Feld zur Übergabe der Verbindungszeichenfolge an den lokalen Speicheremulator deklarieren:

	// Define the connection-string with Azure Storage Emulator.
	const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  

Wählen Sie zum Starten des Azure-Speicheremulators die Schaltfläche **Start** aus, oder drücken Sie die **Windows-Taste**. Geben Sie **Azure-Speicheremulator** ein, und wählen Sie den **Microsoft Azure-Speicheremulator** aus der Liste der Anwendungen aus.

In den folgenden Beispielen wird davon ausgegangen, dass Sie eine dieser zwei Methoden verwendet haben, um die Speicherverbindungszeichenfolge abzurufen.

## Abrufen der Verbindungszeichenfolge  
Sie können Ihre Speicherkontoinformationen mit der Klasse **cloud_storage_account** darstellen. Verwenden Sie zum Abrufen von Speicherkontoinformationen aus der Speicher-Verbindungszeichenfolge die parse-Methode.

	// Retrieve the storage account from the connection string. 
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

Anschließend rufen Sie einen Verweis auf eine **cloud_table_client**-Klasse ab, da Sie mit dieser Verweisobjekte für Tabellen und Entitäten abrufen können, die innerhalb des Tabellenspeicherdiensts gespeichert sind. Mit dem folgenden Code wird unter Verwendung des oben abgerufenen Speicherkontoobjekts ein **cloud_table_client**-Objekt erstellt:

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

## Erstellen einer Tabelle
Mit einem **cloud_table_client**-Objekt können Sie Verweisobjekte für Tabellen und Entitäten abrufen. Durch den folgenden Code wird ein **cloud_table_client**-Objekt erstellt und verwendet, um eine neue Tabelle zu erstellen.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);  

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Create the table if it doesn't exist.
	table.create_if_not_exists();  

## Hinzufügen einer Entität zu einer Tabelle
Um einer Tabelle eine Entität hinzuzufügen, erstellen Sie ein neues **table_entity**-Objekt und übergeben es an **table_operation::insert_entity**. Im folgenden Code wird der Vorname des Kunden als Zeilenschlüssel und der Nachname als Partitionsschlüssel verwendet. In Kombination miteinander wird mit dem Partitions- und Zeilenschlüssel eine Entität in der Tabelle eindeutig identifiziert. Entitäten mit demselben Partitionsschlüssel können schneller abgerufen werden als Entitäten mit unterschiedlichen Partitionsschlüsseln, die Verwendung verschiedener Partitionsschlüssel ermöglicht jedoch eine größere parallele Vorgangsskalierbarkeit. Weitere Informationen finden Sie unter [Checkliste zu Leistung und Skalierbarkeit von Microsoft Azure Storage](./storage-performance-checklist.md/).

Durch den folgenden Code wird eine neue Instanz von **table_entity** mit einigen zu speichernden Kundendaten erstellt. Als Nächstes ruft der Code **table_operation::insert_entity** auf, um ein **table_operation**-Objekt zum Einfügen einer Entität in eine Tabelle zu erstellen, und weist dieser Entität die neue Tabellenentität zu. Schließlich ruft der Code die execute-Methode für das **cloud_table**-Objekt auf. Die neue **table_operation** sendet eine Anforderung an den Speicherdienst, dass die neue Kundenentität in die Tabelle "people" eingefügt werden soll.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Create the table if it doesn't exist.
	table.create_if_not_exists();

	// Create a new customer entity.
	azure::storage::table_entity customer1(utility::string_t(U("Harp")), utility::string_t(U("Walter")));

	azure::storage::table_entity::properties_type& properties = customer1.properties();
	properties.reserve(2);
	properties[U("Email")] = azure::storage::entity_property(utility::string_t(U("Walter@contoso.com")));

	properties[U("Phone")] = azure::storage::entity_property(utility::string_t(U("425-555-0101")));

	// Create the table operation that inserts the customer entity.
	azure::storage::table_operation insert_operation = azure::storage::table_operation::insert_entity(customer1);

	// Execute the insert operation.
	azure::storage::table_result insert_result = table.execute(insert_operation);

## Einfügen eines Entitätsbatchs
Sie können einen Entitätsbatch in einem Schreibvorgang in den Tabellendienst einfügen. Durch den folgenden Code wird ein **table_batch_operation**-Objekt erstellt, dem anschließend drei Einfügevorgänge hinzugefügt werden. Für jeden Einfügevorgang wird ein neues Entitätsobjekt erstellt, dessen Werte werden festgelegt, und dann wird die "insert"-Methode für das **table_batch_operation**-Objekt aufgerufen, um die Entität einem neuen Einfügevorgang zuzuordnen. Anschließend folgt ein Aufruf von **cloud_table.execute**, um den Vorgang auszuführen.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Define a batch operation.
	azure::storage::table_batch_operation batch_operation;

	// Create a customer entity and add it to the table.
	azure::storage::table_entity customer1(utility::string_t(U("Smith")), utility::string_t(U("Jeff")));

	azure::storage::table_entity::properties_type& properties1 = customer1.properties();
	properties1.reserve(2);
	properties1[U("Email")] = azure::storage::entity_property(utility::string_t(U("Jeff@contoso.com")));
	properties1[U("Phone")] = azure::storage::entity_property(utility::string_t(U("425-555-0104")));

	// Create another customer entity and add it to the table.
	azure::storage::table_entity customer2(utility::string_t(U("Smith")), utility::string_t(U("Ben")));

	azure::storage::table_entity::properties_type& properties2 = customer2.properties();
	properties2.reserve(2);
	properties2[U("Email")] = azure::storage::entity_property(utility::string_t(U("Ben@contoso.com")));
	properties2[U("Phone")] = azure::storage::entity_property(utility::string_t(U("425-555-0102")));

	// Create a third customer entity to add to the table.
	azure::storage::table_entity customer3(utility::string_t(U("Smith")), utility::string_t(U("Denise")));

	azure::storage::table_entity::properties_type& properties3 = customer3.properties();
	properties3.reserve(2);
	properties3[U("Email")] = azure::storage::entity_property(utility::string_t(U("Denise@contoso.com")));
	properties3[U("Phone")] = azure::storage::entity_property(utility::string_t(U("425-555-0103")));
		
	// Add customer entities to the batch insert operation.
	batch_operation.insert_or_replace_entity(customer1);
	batch_operation.insert_or_replace_entity(customer2);
	batch_operation.insert_or_replace_entity(customer3);

	// Execute the batch operation.
	std::vector<azure::storage::table_result> results = table.execute_batch(batch_operation);

Beachten Sie im Zusammenhang mit Batchvorgängen Folgendes:

-	Bis zu 100 Einfüge-, Lösch-, Zusammenführungs-, Ersetzungs-, Einfüge- oder Zusammenführungs- und Einfüge- oder Ersetzungsvorgänge können in beliebiger Kombination in einem einzelnen Batch ausgeführt werden.  
-	Ein Batchvorgang kann einen Abfragevorgang enthalten, allerdings nur als einzigen Vorgang in diesem Batch.  
-	Alle Entitäten in einem Batchvorgang müssen über denselben Partitionsschlüssel verfügen.  
-	Ein Batchvorgang ist auf eine Nutzlast von 4 MB Daten beschränkt.  

## Abrufen aller Entitäten in einer Partition
Verwenden Sie ein **table_query**-Objekt, um eine Tabelle für alle Entitäten in einer Partition abzurufen. Im folgenden Codebeispiel wird ein Filter für Entitäten erstellt, wobei "Smith" der Partitionsschlüssel ist. In diesem Beispiel werden die Felder der einzelnen Entitäten in den Abfrageergebnissen an die Konsole ausgegeben.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Construct the query operation for all customer entities where PartitionKey="Smith".
	azure::storage::table_query query;

	query.set_filter_string(azure::storage::table_query::generate_filter_condition(U("PartitionKey"), azure::storage::query_comparison_operator::equal, U("Smith")));

	std::vector<azure::storage::table_entity> entities = table.execute_query(query);

	// Print the fields for each customer.
	for (std::vector<azure::storage::table_entity>::const_iterator it = entities.cbegin(); it != entities.cend(); ++it)
	{
	const azure::storage::table_entity::properties_type& properties = it->properties();

	std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key() << U(", Property1: ") << utility::string_t(properties.at(U("Email")).string_value()) << U(", Property2: ") << utility::string_t(properties.at(U("Phone")).string_value()) << std::endl; }

Die Abfrage in diesem Beispiel gibt alle Entitäten zurück, die den Filterkriterien entsprechen. Wenn Sie über umfangreiche Tabellen verfügen und daher die Tabellenentitäten oft herunterladen müssen, wird empfohlen, die Daten stattdessen in Azure-Speicher-Blobs zu speichern.

## Abrufen eines Entitätsbereichs in einer Partition
Wenn Sie nicht alle Entitäten in einer Partition abrufen möchten, können Sie einen Bereich angeben, indem Sie den Partitionsschlüsselfilter mit einem Zeilenschlüsselfilter kombinieren. Im folgenden Codebeispiel werden zwei Filter eingesetzt, um alle Entitäten in der Partition "Smith" abzurufen, deren Zeilenschlüssel (Vorname) mit einem Buchstaben vor dem Buchstaben E im Alphabet beginnen. Danach werden die Abfrageergebnisse gedruckt.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Create the table query.
	azure::storage::table_query query;

	query.set_filter_string(azure::storage::table_query::combine_filter_conditions(
		azure::storage::table_query::generate_filter_condition(U("PartitionKey"), 
		azure::storage::query_comparison_operator::equal, U("Smith")),
		azure::storage::query_logical_operator::op_and,
		azure::storage::table_query::generate_filter_condition(U("RowKey"), azure::storage::query_comparison_operator::less_than, U("E"))));

	// Execute the query.
	std::vector<azure::storage::table_entity> entities = table.execute_query(query);

	// Loop through the results, displaying information about the entity.
	for (std::vector<azure::storage::table_entity>::const_iterator it = entities.cbegin(); it != entities.cend(); ++it)
	{
		const azure::storage::table_entity::properties_type& properties = it->properties();

		std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
		<< U(", Property1: ") << utility::string_t(properties.at(U("Email")).string_value())
				<< U(", Property2: ") << utility::string_t(properties.at(U("Phone")).string_value()) << std::endl;
	}  

## Abrufen einer einzelnen Entität
Sie können eine Abfrage schreiben, um eine bestimmte Entität abzurufen. Im folgenden Code wird der Kunde "Jeff Smith" durch eine **table_operation::retrive_entity** angegeben. Bei dieser Methode wird nur eine Entität anstelle einer Sammlung zurückgegeben, und der zurückgegebene Wert befindet sich in **table_result**. Die Angabe beider Schlüssel, Partition und Zeile, in einer Abfrage ist die schnellste Möglichkeit, um eine einzelne Entität aus dem Tabellendienst abzurufen.

	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Retrieve the entity with partition key of "Smith" and row key of "Jeff".
	azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
	azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

	// Output the entity.
	azure::storage::table_entity entity = retrieve_result.entity();
	const azure::storage::table_entity::properties_type& properties = entity.properties();

	std::wcout << U("PartitionKey: ") << entity.partition_key() << U(", RowKey: ") << entity.row_key() << U(", Property1: ") << utility::string_t(properties.at(U("Email")).string_value())
	<< U(", Property2: ") << utility::string_t(properties.at(U("Phone")).string_value()) << std::endl;  

## Ersetzen einer Entität
Um eine Entität zu ersetzen, rufen Sie sie aus dem Tabellendienst ab, ändern Sie das Entitätsobjekt, und speichern Sie die Änderungen dann im Tabellendienst. Mit dem folgenden Code werden Telefonnummer und E-Mail-Adresse eines vorhandenen Kunden geändert. Statt **table_operation:: insert_entity** wird in diesem Codebeispiel **table_operation:: replace_entity** aufgerufen. Dadurch wird die Entität auf dem Server vollständig ersetzt, sofern die Entität auf dem Server sich seit dem letzten Abruf nicht geändert hat. In diesem Fall wäre der Vorgang nicht erfolgreich. Auf diese Weise soll verhindert werden, dass eine Änderung, die zwischen dem Abruf und der Aktualisierung durch eine andere Komponente der Anwendung vorgenommen wurde, unabsichtlich überschrieben wird. Die richtige Vorgehensweise in diesem Fall besteht darin, die Entität erneut abzurufen, Ihre Änderungen vorzunehmen (falls diese noch gültig sind) und dann einen anderen **table_operation::replace_entity**-Vorgang auszuführen. Im nächsten Abschnitt erfahren Sie, wie Sie dieses Verhalten überschreiben.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Replace an entity.
	azure::storage::table_entity entity_to_replace(U("Smith"), U("Jeff"));
	azure::storage::table_entity::properties_type& properties_to_replace = entity_to_replace.properties();
	properties_to_replace.reserve(2);

	// Specify a new phone number.
	properties_to_replace[U("Phone")] = azure::storage::entity_property(utility::string_t(U("425-555-0106")));

	// Specify a new email address.
	properties_to_replace[U("Email")] = azure::storage::entity_property(utility::string_t(U("JeffS@contoso.com")));

	// Create an operation to replace the entity.
	azure::storage::table_operation replace_operation = azure::storage::table_operation::replace_entity(entity_to_replace);

	// Submit the operation to the table service.
	azure::storage::table_result replace_result = table.execute(replace_operation);

## Einfügen oder Ersetzen einer Entität
**table_operation::replace_entity**-Vorgänge sind nicht erfolgreich, wenn die Entität seit dem letzten Abruf vom Server geändert wurde. Darüber hinaus müssen Sie zuerst die Entität vom Server abrufen, damit der **table_operation::replace_entity**-Vorgang erfolgreich ist. Manchmal ist jedoch nicht bekannt, ob die Entität auf dem Server vorhanden ist, und die darin aktuell gespeicherten Werte sind nicht relevant. In diesem Fall sollten diese durch die Aktualisierung vollständig überschrieben werden. Dies erreichen Sie mit einem **table_operation::insert_or_replace_entity**-Vorgang. Bei diesem Vorgang wird die Entität eingefügt, falls sie nicht vorhanden ist, oder ersetzt, falls sie vorhanden ist, unabhängig davon, wann die letzte Aktualisierung stattgefunden hat. Im folgenden Codebeispiel wird die Kundenentität für Jeff Smith abgerufen, sie wird danach jedoch mithilfe von **table_operation::insert_or_replace_entity** wieder auf dem Server gespeichert. Alle Änderungen, die zwischen dem Abruf- und Aktualisierungsvorgang an der Entität vorgenommen wurden, werden überschrieben.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Insert-or-replace an entity.
	azure::storage::table_entity entity_to_insert_or_replace(U("Smith"), U("Jeff"));
	azure::storage::table_entity::properties_type& properties_to_insert_or_replace = entity_to_insert_or_replace.properties();

	properties_to_insert_or_replace.reserve(2);

	// Specify a phone number.
	properties_to_insert_or_replace[U("Phone")] = azure::storage::entity_property(utility::string_t(U("425-555-0107")));

	// Specify an email address.
	properties_to_insert_or_replace[U("Email")] = azure::storage::entity_property(utility::string_t(U("Jeffsm@contoso.com")));

	// Create an operation to insert or replace the entity.
	azure::storage::table_operation insert_or_replace_operation = azure::storage::table_operation::insert_or_replace_entity(entity_to_insert_or_replace);

	// Submit the operation to the table service.
	azure::storage::table_result insert_or_replace_result = table.execute(insert_or_replace_operation);
 
## Abfragen einer Teilmenge von Entitätseigenschaften  
Mit einer Abfrage einer Tabelle können nur einige wenige Eigenschaften einer Entität aufgerufen werden. Die Abfrage im folgenden Code übergibt mit der **table_query::set_select_columns**-Methode nur die E-Mail-Adressen von Entitäten in der Tabelle.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Define the query, and only select the Email property.
	azure::storage::table_query query;
	std::vector<utility::string_t> columns;

	columns.push_back(U("Email"));
	query.set_select_columns(columns);
	std::vector<azure::storage::table_entity> entities = table.execute_query(query);

	// Display the results.
	for (std::vector<azure::storage::table_entity>::const_iterator it = entities.cbegin(); it != entities.cend(); ++it)
	{
    	const azure::storage::table_entity::properties_type& properties = it->properties();
    	std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key();
			
	for (auto prop_it = properties.begin(); prop_it != properties.end(); ++prop_it) { std::wcout << ", " << prop_it->first << ": " << prop_it->second.str(); } std::wcout << std::endl; }

**Hinweis:** Das Abfragen einiger Eigenschaften einer Entität ist effizienter als das Abrufen aller Eigenschaften.

## Löschen einer Entität
Sie können eine Entität problemlos nach dem Abrufen löschen. Nachdem die Entität abgerufen wurde, rufen Sie die **table_operation::delete_entity** mit der zu löschenden Entität auf. Rufen Sie anschließend eine Ausführung der **cloud_table.execute**-Methode auf. Der folgende Code ruft eine Entität mit Partitionsschlüssel "Smith" sowie Zeilenschlüssel "Jeff" ab und löscht sie.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
	azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
	azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

	// Create an operation to delete the entity.
	azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

	// Submit the delete operation to the table service.
	azure::storage::table_result delete_result = table.execute(delete_operation);  
 
## Löschen einer Tabelle
Schließlich wird mit dem folgenden Codebeispiel eine Tabelle aus einem Speicherkonto gelöscht. Eine gelöschte Tabelle kann für eine bestimmte Zeitdauer nach dem Löschvorgang nicht neu erstellt werden.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
	azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
	azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

	// Create an operation to delete the entity.
	azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

	// Submit the delete operation to the table service.
	azure::storage::table_result delete_result = table.execute(delete_operation);

## Nächste Schritte
Nachdem Sie sich nun mit den Grundlagen von Tabellenspeichern vertraut gemacht haben, lesen Sie die folgenden Artikel, um mehr über Azure-Speicher zu erfahren.

-	[Verwenden des Blob-Speichers mit C++](storage-c-plus-plus-how-to-use-blobs.md)
-	[Verwenden des Warteschlangenspeichers in C++](storage-c-plus-plus-how-to-use-queues.md)
-	[Speicherclientbibliothek für C++](https://msdn.microsoft.com/library/azure/gg433040.aspx) 
-	[MSDN-Referenz für Azure-Speicher](https://msdn.microsoft.com/library/azure/gg433040.aspx)
-	[Azure-Speicherdokumentation](http://azure.microsoft.com/documentation/services/storage/)

<!--HONumber=52-->
