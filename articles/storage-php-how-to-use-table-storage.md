<properties 
	pageTitle="Verwenden des Tabellenspeichers (PHP) | Microsoft Azure" 
	description="Erfahren Sie, wie Sie den Tabellendienst aus PHP zum Erstellen und Löschen von Tabellen sowie zum Einfügen und Abfragen in der Tabelle verwenden." 
	services="storage" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="tomfitz"/>

# Verwenden des Tabellendiensts von PHP

Dieser Leitfaden erläutert Ihnen das Durchführen allgemeiner Szenarios mit dem Azure-Tabellendienst. Die Beispiele sind in PHP geschrieben und verwenden das [Azure SDK für PHP][download]. Die behandelten Szenarien umfassen das **Erstellen und Löschen einer Tabelle sowie das Einfügen, Löschen und Abfragen von Entitäten in einer Tabelle**. Weitere Informationen zum Azure Tabellendienst, siehe [Nächste Schritte](#NextSteps) .

##Inhaltsverzeichnis

* [Was ist der Tabellendienst](#what-is)
* [Konzepte](#concepts)
* [Erstellen eines Azure-Speicherkontos](#CreateAccount)
* [Erstellen einer PHP-Anwendung](#CreateApplication)
* [Für Zugriff auf Tabellendienst, Anwendung konfigurieren](#ConfigureStorage)
* [Einrichten einer Azure-Speicherverbindung](#ConnectionString)
* [Vorgehensweise: Erstellen einer Tabelle](#CreateTable)
* [Vorgehensweise: Hinzufügen einer Entität zu einer Tabelle](#AddEntity)
* [Vorgehensweise: Abrufen einer einzelnen Entität](#RetrieveEntity)
* [Vorgehensweise: Abrufen aller Entitäten in einer Partition](#RetEntitiesInPartition)
* [Vorgehensweise Abrufen einer Teilmenge von Entitäten in einer Partition](#RetrieveSubset)
* [Vorgehensweise: Abrufen einer Teilmenge von Entitätseigenschaften](#RetPropertiesSubset)
* [Vorgehensweise: Aktualisieren einer Entität](#UpdateEntity)
* [Vorgehensweise: Batch-Tabellenvorgänge](#BatchOperations)
* [Vorgehensweise: Löschen einer Tabelle](#DeleteTable)
* [Nächste Schritte](#NextSteps)

[AZURE.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

##<a id="CreateAccount"></a>Erstellen eines Azure-Speicherkontos

[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]

##<a id="CreateApplication"></a>Erstellen einer PHP-Anwendung

Die einzige Voraussetzung für das Erstellen einer PHP-Anwendung, die auf den Azure-Tabellendienst zugreift, ist das Referenzieren von Klassen im Azure-SDK für PHP innerhalb des Codes. Sie können die Anwendung mit beliebigen Entwicklungstools erstellen, unter anderem auch mit Notepad.

In diesem Leitfaden verwenden Sie Tabellendienstfunktionen, die lokal aus einer PHP-Anwendung heraus oder im Code, der in einer Azure-Webrolle, -Workerrolle oder -Website ausgeführt wird, aufgerufen werden.

##<a id="GetClientLibrary"></a>Zu den Azure-Clientbibliotheken

[AZURE.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

##<a id="ConfigureStorage"></a>Anwendung konfigurieren, um auf den Tabellendienst zuzugreifen

Um die APIs des Azure-Tabellendiensts zu verwenden, müssen Sie folgendermaßen vorgehen:

1. Verweisen Sie mithilfe der [require_once][require_once]-Anweisung auf die Autoloaderdatei, und
2. Verweisen Sie auf alle Klassen, die Sie möglicherweise verwenden.

Das folgende Beispiel zeigt, wie die Autoloaderdatei integriert, und auf die **ServicesBuilder**-Klasse verwiesen werden kann.

> [AZURE.NOTE]
> In diesem Beispiel (und in anderen Beispielen in diesem Artikel) wird angenommen, dass Sie die PHP-Clientbibliotheken für Azure über Composer installiert haben. Wenn Sie die Bibliotheken manuell oder als PEAR-Paket installiert haben, müssen Sie auf die<code>WindowsAzure.php</code>-Autoloaderdatei verweisen.

	require_once 'vendor\autoload.php';
	use WindowsAzure\Common\ServicesBuilder;


In den folgenden Beispielen wird die `require_once` Anweisung immer angezeigt, jedoch wird nur auf die für die Ausführung des Beispiels erforderlichen Klassen verwiesen.

##<a id="ConnectionString"></a>Einrichten einer Azure-Speicherverbindung

Um einen Azure-Tabellendienstclient zu instanzieren, benötigen Sie zunächst eine gültige Verbindungszeichenfolge. Das Format der Tabellendienst-Verbindungszeichenfolge ist folgendermaßen:

Für den Zugriff auf einen Livedienst:

	DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Für den Zugriff auf den Emulatorspeicher:

	UseDevelopmentStorage=true


Um einen Azure-Dienstclient zu erstellen, nutzen Sie die **ServicesBuilder**-Klasse. Sie können:

* Die Verbindungszeichenfolge direkt an die Klasse weitergeben oder
* den **CloudConfigurationManager (CCM)** verwenden, um mehrere externe Quellen für die Verbindungszeichenfolge zu überprüfen:
	* Standardmäßig wird eine externe Quelle unterstützt - Umgebungsvariablen
	* neue Datenquellen können über die Erweiterung der **ConnectionStringSource**-Klasse hinzugefügt werden

Für die hier erläuterten Beispiele wird die Verbindungszeichenfolge direkt weitergegeben.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;

	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);


##<a id="CreateTable"></a>Vorgehensweise: Erstellen einer Tabelle

Ein **TableRestProxy**-Objekt ermöglicht Ihnen das Erstellen einer Tabelle mit der **CreateTable**-Methode. Wenn Sie eine Tabelle erstellen, können Sie das Tabellendiensttimeout festlegen. (Weitere Informationen zum Tabellendiensttimeout finden Sie unter [Festlegen von Timeouts für Tabellendienstvorgänge][table-service-timeouts].)

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

	try	{
		// Create table.
		$tableRestProxy->createTable("mytable");
	}
	catch(ServiceException $e){
		$code = $e->getCode();
		$error_message = $e->getMessage();
		// Handle exception based on error codes and messages.
		// Error codes and messages can be found here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179438.aspx
	}

Weitere Informationen zu Einschränkungen für Tabellennamen finden Sie unter [Grundlagen des Tabellendienst-Datenmodells][table-data-model].

##<a id="AddEntity"></a>Vorgehensweise: Hinzufügen einer Entität zu einer Tabelle

Um eine Entität zu einer Tabelle hinzuzufügen, erstellen Sie ein neues **Entität**-Objekt und übergeben Sie es an **TableRestProxy->insertEntity**. Beachten Sie, dass Sie beim Erstellen einer Entität einen `PartitionKey` und `RowKey` festlegen müssen. Dabei handelt es sich um eindeutige Kennzeichner für eine Entität und um Werte, die viel schneller als andere Entitätseigenschaften abgefragt werden können. Das System verwendet `PartitionKey`, um die Entitäten der Tabelle automatisch auf viele Speicherknoten zu verteilen. Entitäten mit dem gleichen `PartitionKey` werden auf dem gleichen Knoten gespeichert. (Vorgänge für mehrere Entitäten, die auf dem gleichen Knoten gespeichert sind, haben eine höhere Leistung als Vorgänge für Entitäten, die auf verschiedenen Knoten gespeichert sind.) Die `RowKey` ist die eindeutige ID einer Entität innerhalb einer Partition.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Table\Models\Entity;
	use WindowsAzure\Table\Models\EdmType;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	$entity = new Entity();
	$entity->setPartitionKey("tasksSeattle");
	$entity->setRowKey("1");
	$entity->addProperty("Description", null, "Take out the trash.");
	$entity->addProperty("DueDate", 
						 EdmType::DATETIME, 
						 new DateTime("2012-11-05T08:15:00-08:00"));
	$entity->addProperty("Location", EdmType::STRING, "Home");
	
	try{
		$tableRestProxy->insertEntity("mytable", $entity);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
	}

Informationen zu Tabelleneigenschaften und-Typen finden Sie unter [Grundlagen des Tabellendienst-Datenmodells][table-data-model].

Die **TableRestProxy**-Klasse bietet zwei alternative Methoden zum Einfügen von Entitäten: **insertOrMergeEntity** und **insertOrReplaceEntity**. Um diese Methoden zu verwenden, erstellen Sie eine neue **Entität** und geben Sie diese als Parameter an beide Methoden weiter. Jede Methode fügt die Entität ein, falls sie nicht vorhanden ist. Wenn die Entität bereits vorhanden ist, werden über **insertOrMergeEntity** die Eigenschaftswerte aktualisiert, sofern sie bereits vorhanden sind. Wenn keine Eigenschaftswerte vorhanden sind, werden diese hinzugefügt. **insertOrReplaceEntity** hingegen ersetzt eine bestehende Entität vollständig. Das folgende Beispiel zeigt, wie Sie **insertOrMergeEntity** nutzen können. Wenn die Entität mit dem `PartitionKey` "tasksSeattle" und `RowKey` "1" noch nicht vorhanden ist, wird sie eingefügt. Wenn sie jedoch zuvor eingefügt wurde (wie im obigen Beispiel gezeigt), aktualisiert sich die `DueDate`-Eigenschaft und die `Status`-Eigenschaft wird hinzugefügt. Die `Description`- und `Location`-Eigenschaften werden auch aktualisiert. Allerdings mit Werten, mit denen sie effektiv unverändert bleiben. Falls die letzten zwei Eigenschaft nicht wie im Beispiel gezeigt hinzugefügt würden, sondern auf der Zielentität vorhanden wären, blieben ihre vorhandenen Werte unverändert.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Table\Models\Entity;
	use WindowsAzure\Table\Models\EdmType;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	//Create new entity.
	$entity = new Entity();
	
	// PartitionKey and RowKey are required.
	$entity->setPartitionKey("tasksSeattle");
	$entity->setRowKey("1");
	
	// If entity exists, existing properties are updated with new values and
	// new properties are added. Missing properties are unchanged.
	$entity->addProperty("Description", null, "Take out the trash.");
	$entity->addProperty("DueDate", EdmType::DATETIME, new DateTime()); // Modified the DueDate field.
	$entity->addProperty("Location", EdmType::STRING, "Home");
	$entity->addProperty("Status", EdmType::STRING, "Complete"); // Added Status field.
	
	try	{
		// Calling insertOrReplaceEntity, instead of insertOrMergeEntity as shown,
		// would simply replace the entity with PartitionKey "tasksSeattle" and RowKey "1".
		$tableRestProxy->insertOrMergeEntity("mytable", $entity);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
	   

##<a id="RetrieveEntity"></a>Vorgehensweise: Abrufen einer einzelnen Entität

Mit der **TableRestProxy->getEntity**-Methode können Sie eine einzelne Entität abrufen, indem Sie deren `PartitionKey` und `RowKey` abfragen. Im Beispiel unten werden der Partitionsschlüssel `tasksSeattle` und der Zeilenschlüssel '1' an die **getEntity**-Methode übergeben.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	try	{
		$result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
	
	$entity = $result->getEntity();

	echo $entity->getPartitionKey().":".$entity->getRowKey();

##<a id="RetEntitiesInPartition"></a>Vorgehensweise: Abrufen aller Entitäten in einer Partition

Entity-Abfragen werden mithilfe von Filtern erstellt (Weitere Informationen finden Sie unter [Abfragen von Tabellen und Entitäten][filters]). Um alle Entitäten in der Partition abzurufen, verwenden Sie den Filter "PartitionKey Eq *partition_name*". Das folgende Beispiel zeigt das Abrufen aller Entitäten in der `tasksSeattle` Partition durch Übergeben eines Filters auf die **queryEntities**-Methode.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	$filter = "PartitionKey eq 'tasksSeattle'";
	
	try	{
		$result = $tableRestProxy->queryEntities("mytable", $filter);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
	
	$entities = $result->getEntities();
	
	foreach($entities as $entity){
		echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
	}

##<a id="RetrieveSubset"></a>Vorgehensweise: Abrufen einer Teilmenge von Entitäten in einer Partition

Das Muster, das im vorherigen Beispiel verwendet wird, kann auch verwendet werden, um eine Teilmenge der Entitäten in einer Partition abzurufen. Die Teilmenge der Entitäten, die Sie abrufen, richtet sich nach dem Filter, den Sie verwenden (Weitere Informationen finden Sie unter [Abfragen von Tabellen und Entitäten][Filter]). Das folgende Beispiel zeigt, wie Sie einen Filter zum Abrufen aller Entitäten eines bestimmten `Speicherorts` und eines `DueDate` kleiner als ein angegebenes Datum nutzen können.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	$filter = "Location eq 'Office' and DueDate lt '2012-11-5'";
	
	try	{
		$result = $tableRestProxy->queryEntities("mytable", $filter);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
	
	$entities = $result->getEntities();
	
	foreach($entities as $entity){
		echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
	}

##<a id="RetPropertiesSubset"></a>Vorgehensweise: Abrufen einer Teilmenge von Entitätseigenschaften

Eine Abfrage kann eine Teilmenge der Entitätseigenschaften abrufen. Diese Technik namens *-Projektion* reduziert die Bandbreite und trägt dazu bei, die Abfrageleistung zu steigern, vor allem bei großen Entitäten. Um eine Eigenschaft, die abgerufen werden soll, anzugeben, übergeben Sie den Namen der Eigenschaft an die **Query->addSelectField**-Methode. Sie können diese Methode mehrfach aufrufen, um weitere Eigenschaften hinzuzufügen. Nach der Ausführung von **TableRestProxy->queryEntities**, verfügen die zurückgegebenen Entitäten nur über die ausgewählten Eigenschaften. (Wenn Sie eine Teilmenge der Tabellenentitäten zurückgeben möchten, verwenden Sie einen Filter, wie in den Abfragen oben gezeigt wird.)

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Table\Models\QueryEntitiesOptions;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	$options = new QueryEntitiesOptions();
	$options->addSelectField("Description");
	
	try	{
		$result = $tableRestProxy->queryEntities("mytable", $options);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
	
	// All entities in the table are returned, regardless of whether 
	// they have the Description field.
	// To limit the results returned, use a filter.
	$entities = $result->getEntities();

	foreach($entities as $entity){
		$description = $entity->getProperty("Description")->getValue();
		echo $description."<br />";
	}

##<a id="UpdateEntity"></a>Vorgehensweise: Aktualisieren einer Entität

Eine vorhandene Entität kann mithilfe der **Entity->setProperty** und **Entity->addProperty**-Methoden aktualisiert werden, indem anschließend **TableRestProxy->updateEntity** aufgerufen wird. Das folgende Beispiel ruft eine Entität ab, ändert eine Eigenschaft, entfernt eine andere Eigenschaft und fügt eine neue Eigenschaft hinzu. Beachten Sie, dass das Entfernen einer Eigenschaft erfolgt, indem Sie ihren Wert auf **Null** setzen. 

	require_once 'vendor\autoload.php';
	
	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Table\Models\Entity;
	use WindowsAzure\Table\Models\EdmType;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	$result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);
	
	$entity = $result->getEntity();
	
	$entity->setPropertyValue("DueDate", new DateTime()); //Modified DueDate.
	
	$entity->setPropertyValue("Location", null); //Removed Location.
	
	$entity->addProperty("Status", EdmType::STRING, "In progress"); //Added Status.

	try	{
		$tableRestProxy->updateEntity("mytable", $entity);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

##<a id="DeleteEntity"></a>Vorgehensweise: Löschen einer Entität

Um eine Entität zu löschen, übergeben Sie den Tabellennamen sowie  `PartitionKey` und `RowKey` der Entität an die **TableRestProxy->deleteEntity**-Methode.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	try	{
		// Delete entity.
		$tableRestProxy->deleteEntity("mytable", "tasksSeattle", "2");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Beachten Sie, dass Sie für Parallelitätsüberprüfungen das Etag für eine zu löschende Entität über die **DeleteEntityOptions->setEtag**-Methode einstellen können, indem Sie das **deleteEntityOptions**-Objekt als vierten Parameter an **deleteEntity** weitergeben.

##<a id="BatchOperations"></a>Vorgehensweise: Batch-Tabellenvorgänge

Mit der **TableRestProxy->batch**-Methode können Sie mehrere Vorgänge in einer einzelnen Anforderung ausführen. Das Muster umfasst hier das Hinzufügen von Vorgängen zum **BatchRequest**-Objekt und das anschließende Übergeben des **BatchRequest**-Objekts auf die **TableRestProxy->batch**-Methode. Um einen Vorgang zu einem **BatchRequest**-Objekt hinzuzufügen, können Sie eine der folgenden Methoden mehrfach aufrufen:

* **addInsertEntity** (fügt einen insertEntity-Vorgang hinzu)
* **addUpdateEntity** (fügt einen updateEntity-Vorgang hinzu)
* **addMergeEntity** (fügt einen mergeEntity-Vorgang hinzu)
* **addInsertOrReplaceEntity** (fügt einen insertOrReplaceEntity-Vorgang hinzu)
* **addInsertOrMergeEntity** (fügt einen dnsertOrMergeEntity-Vorgang hinzu)
* **addDeleteEntity** (fügt einen deleteEntity-Vorgang hinzu)

Das folgende Beispiel zeigt, wie **insertEntity** und **deleteEntity**-Vorgänge in einer einzelnen Anforderung ausgeführt werden können:

	require_once 'vendor\autoload.php';
	
	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Table\Models\Entity;
	use WindowsAzure\Table\Models\EdmType;
	use WindowsAzure\Table\Models\BatchOperations;

 	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	// Create list of batch operation.
	$operations = new BatchOperations();
	
	$entity1 = new Entity();
	$entity1->setPartitionKey("tasksSeattle");
	$entity1->setRowKey("2");
	$entity1->addProperty("Description", null, "Clean roof gutters.");
	$entity1->addProperty("DueDate", 
						  EdmType::DATETIME, 
						  new DateTime("2012-11-05T08:15:00-08:00"));
	$entity1->addProperty("Location", EdmType::STRING, "Home");
	
	// Add operation to list of batch operations.
    $operations->addInsertEntity("mytable", $entity1);

	// Add operation to list of batch operations.
	$operations->addDeleteEntity("mytable", "tasksSeattle", "1");
	
	try	{
		$tableRestProxy->batch($operations);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Weitere Informationen zur Batchverarbeitung der Tabellenvorgänge finden Sie unter [Ausführen von Entitätsgruppentransaktionen][entity-group-transactions].

##<a id="DeleteTable"></a>Vorgehensweise: Löschen einer Tabelle

Um schließlich eine Tabelle zu löschen, übergeben Sie den Tabellennamen an die **TableRestProxy->deleteTable**-Methode.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	try	{
		// Delete table.
		$tableRestProxy->deleteTable("mytable");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

##<a id="NextSteps"></a>Nächste Schritte

Da Sie jetzt die Grundlagen des Azure-Tabellendiensts erlernt haben, folgen Sie diesen Links, um Informationen zum Ausführen komplexerer Speicheraufgaben zu erhalten.

- Weitere Informationen finden Sie in der MSDN-Referenz: [Speichern und Zugreifen auf Daten in Azure] []
- Besuchen Sie den Blog des Azure-Speicherteams: <http://blogs.msdn.com/b/windowsazurestorage/>

[Herunterladen]: http://go.microsoft.com/fwlink/?LinkID=252473
[Speichern und Zugreifen auf Daten in Azure]: http://msdn.microsoft.com/library/windowsazure/gg433040.aspx
[require_once]: http://php.net/require_once
[Tabellendiensttimeouts]: http://msdn.microsoft.com/library/windowsazure/dd894042.aspx

[Datenmodell der Tabelle]: http://msdn.microsoft.com/library/windowsazure/dd179338.aspx
[Filter]: http://msdn.microsoft.com/library/windowsazure/dd894031.aspx
[Entitätsgruppentransaktionen]: http://msdn.microsoft.com/library/windowsazure/dd894038.aspx
<!--HONumber=42-->
