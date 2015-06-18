<properties 
	pageTitle="Verwenden des Tabellenspeichers mit PHP | Microsoft Azure" 
	description="Erfahren Sie, wie Sie den Tabellenspeicherdienst mit PHP zum Erstellen und Löschen von Tabellen sowie zum Einfügen und Abfragen von Tabellendaten verwenden." 
	services="storage" 
	documentationCenter="php" 
	authors="tfitzmac,tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="03/11/2015" 
	ms.author="tomfitz"/>


# Verwenden des Tabellenspeichers mit PHP

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

## Übersicht

Dieser Leitfaden erläutert das Durchführen allgemeiner Szenarien mit dem Azure-Tabellenspeicherdienst. Die Beispiele wurden in PHP geschrieben und verwenden das [Azure SDK für PHP][Herunterladen]. Die behandelten Szenarien umfassen das **Erstellen und Löschen einer Tabelle sowie das Einfügen, Löschen und Abfragen von Entitäten in einer Tabelle**. Weitere Informationen zum Azure-Tabellenspeicherdienst finden Sie unter [Nächste Schritte](#NextSteps) .

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Erstellen einer PHP-Anwendung

Die einzige Voraussetzung für das Erstellen einer PHP-Anwendung, die auf den Azure-Tabellenspeicherdienst zugreift, ist das Referenzieren von Klassen im Azure SDK für PHP innerhalb des Codes. Sie können die Anwendung mit beliebigen Entwicklungstools erstellen, unter anderem auch mit Editor.

In diesem Leitfaden verwenden Sie Tabellenspeicherdienst-Funktionen, die lokal aus einer PHP-Anwendung heraus oder im Code, der in einer Azure-Webrolle, -Workerrolle oder -Website ausgeführt wird, aufgerufen werden.

## Abrufen der Azure-Clientbibliotheken

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## Konfigurieren der Anwendung für den Zugriff auf den Tabellenspeicherdienst

Um die APIs des Azure-Tabellenspeicherdiensts zu verwenden, müssen Sie folgendermaßen vorgehen:

1. Verweisen Sie mithilfe der [require_once][require_once]-Anweisung auf die Autoloaderdatei und
2. Verweisen Sie auf alle Klassen, die Sie möglicherweise verwenden.

Das folgende Beispiel zeigt, wie die Autoloaderdatei eingeschlossen und die **ServicesBuilder**-Klasse referenziert wird.

> [AZURE.NOTE] In diesem Beispiel (und in anderen Beispielen in diesem Artikel) wird angenommen, dass Sie die PHP-Clientbibliotheken für Azure über Composer installiert haben. Wenn Sie die Bibliotheken manuell oder als PEAR-Paket installiert haben, müssen Sie auf die Autoloaderdatei <code>WindowsAzure.php</code> verweisen.

	require_once 'vendor\autoload.php';
	use WindowsAzure\Common\ServicesBuilder;


In den Beispielen weiter unten wird die  `require_once`-Anweisung immer angezeigt. Jedoch wird nur auf die für die Ausführung des Beispiels erforderlichen Klassen verwiesen.

## Einrichten einer Azure-Speicherverbindung

Um einen Azure-Tabellenspeicherdienst-Client zu instanziieren, benötigen Sie zunächst eine gültige Verbindungszeichenfolge. Das Format der Verbindungszeichenfolge des Tabellenspeicherdiensts ist folgendermaßen:

Für den Zugriff auf einen Livedienst:

	DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Für den Zugriff auf den Emulatorspeicher:

	UseDevelopmentStorage=true


Um einen Azure-Dienstclient zu erstellen, müssen Sie die **ServicesBuilder**-Klasse verwenden. Sie können:

* Die Verbindungszeichenfolge direkt an die Klasse weitergeben oder
* den **CloudConfigurationManager (CCM)** verwenden, um mehrere externe Quellen für die Verbindungszeichenfolge zu überprüfen:
	* Standardmäßig wird eine externe Quelle unterstützt - Umgebungsvariablen
	* neue Datenquellen können über die Erweiterung der **ConnectionStringSource**-Klasse hinzugefügt werden

Für die hier erläuterten Beispiele wird die Verbindungszeichenfolge direkt weitergegeben.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;

	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);


## Vorgehensweise: Erstellen einer Tabelle

Ein **TableRestProxy**-Objekt ermöglicht Ihnen das Erstellen einer Tabelle mit der **CreateTable**-Methode. Wenn Sie eine Tabelle erstellen, können Sie das Tabellenspeicherdienst-Timeout festlegen. (Weitere Informationen zum Timeout des Tabellenspeicherdiensts finden Sie unter [Festlegen von Timeouts für Tabellenspeicherdienst-Vorgänge][table-service-timeouts].)

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
		// http://msdn.microsoft.com/library/azure/dd179438.aspx
	}

Informationen zu Einschränkungen für Tabellennamen finden Sie unter [Grundlegendes zum Tabellenspeicherdienst-Datenmodell][table-data-model].

## Vorgehensweise: Hinzufügen einer Entität zu einer Tabelle

Um einer Tabelle eine Entität hinzuzufügen, erstellen Sie ein neues **Entity**-Objekt und übergeben es an **TableRestProxy->insertEntity**. Beachten Sie, dass Sie beim Erstellen einer Entität einen  `PartitionKey` und  `RowKey` angeben müssen. Dabei handelt es sich um eindeutige Kennzeichner für eine Entität und um Werte, die viel schneller als andere Entitätseigenschaften abgefragt werden können. Das System verwendet  `PartitionKey`, um die Entitäten der Tabelle automatisch über viele Speicherknoten zu verteilen. Entitäten mit dem gleichen  `PartitionKey` werden auf dem gleichen Knoten gespeichert. (Vorgänge für mehrere Entitäten, die auf dem gleichen Knoten gespeichert sind, haben eine höhere Leistung als Vorgänge für Entitäten, die auf verschiedenen Knoten gespeichert sind.) Der  `RowKey` ist die eindeutige ID einer Entität innerhalb einer Partition.

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
		// http://msdn.microsoft.com/library/azure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
	}

Informationen zu Tabelleneigenschaften und -typen finden Sie unter [Grundlegendes zum Tabellenspeicherdienst-Datenmodell][table-data-model].

Die **TableRestProxy**-Klasse bietet zwei alternative Methoden zum Einfügen von Entitäten: **insertOrMergeEntity** und **insertOrReplaceEntity**. Um diese Methoden zu verwenden, erstellen Sie ein neues **Entity**-Objekt und übergeben es als Parameter an eine Methode. Jede Methode fügt die Entität ein, falls sie nicht vorhanden ist. Wenn die Entität bereits vorhanden ist, werden über **insertOrMergeEntity** die Eigenschaftswerte aktualisiert, sofern sie bereits vorhanden sind. Wenn keine Eigenschaftswerte vorhanden sind, werden diese hinzugefügt. **insertOrReplaceEntity** hingegen ersetzt eine bestehende Entität vollständig. Das folgende Beispiel zeigt, wie Sie **insertOrMergeEntity** nutzen können. Wenn die Entität mit dem  `PartitionKey` "tasksSeattle" und  `RowKey` "1" noch nicht vorhanden ist, wird sie eingefügt. Falls sie jedoch zuvor eingefügt wurde (wie in dem Beispiel oben gezeigt), wird die  `DueDate`-Eigenschaft aktualisiert und die  `Status`-Eigenschaft hinzugefügt. Die `Description`- und `Location`-Eigenschaften werden auch aktualisiert. Allerdings mit Werten, mit denen sie effektiv unverändert bleiben. Falls die letzten zwei Eigenschaft nicht wie im Beispiel gezeigt hinzugefügt würden, sondern auf der Zielentität vorhanden wären, blieben ihre vorhandenen Werte unverändert.

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
		// http://msdn.microsoft.com/library/azure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
	   

## Vorgehensweise: Abrufen einer einzelnen Entität

Mit der **TableRestProxy->getEntity**-Methode können Sie eine einzelne Entität abrufen, indem Sie deren  `PartitionKey` und  `RowKey` abfragen. Im Beispiel unten werden der Partitionsschlüssel  `tasksSeattle` und der Zeilenschlüssel '1' an die **getEntity**-Methode übergeben.

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
		// http://msdn.microsoft.com/library/azure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
	
	$entity = $result->getEntity();

	echo $entity->getPartitionKey().":".$entity->getRowKey();

## Vorgehensweise: Abrufen aller Entitäten in einer Partition

Entitätsabfragen werden mithilfe von Filtern erstellt (weitere Informationen finden Sie unter [Abfragen von Tabellen und Entitäten][Filter]). Um alle Entitäten in der Partition abzurufen, verwenden Sie den Filter "PartitionKey Eq *partition_name*". Das folgende Beispiel zeigt, wie alle Entitäten in der Partition  `tasksSeattle` durch Übergeben eines Filters an die **queryEntities**-Methode abgerufen werden.

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
		// http://msdn.microsoft.com/library/azure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
	
	$entities = $result->getEntities();
	
	foreach($entities as $entity){
		echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
	}

## Vorgehensweise: Abrufen einer Teilmenge von Entitäten in einer Partition

Das Muster, das im vorherigen Beispiel verwendet wird, kann auch verwendet werden, um eine Teilmenge der Entitäten in einer Partition abzurufen. Die Teilmenge der Entitäten, die Sie abrufen, wird durch den verwendeten Filter bestimmt (weitere Informationen finden Sie unter [Abfragen von Tabellen und Entitäten][filters]). Das folgende Beispiel zeigt, wie ein Filter zum Abrufen aller Entitäten mit einem bestimmten Wert für  `Location` und einem  `DueDate` vor einem angegebenen Datum verwendet wird.

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
		// http://msdn.microsoft.com/library/azure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
	
	$entities = $result->getEntities();
	
	foreach($entities as $entity){
		echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
	}

## Vorgehensweise: Abrufen einer Teilmenge von Entitätseigenschaften

Eine Abfrage kann eine Teilmenge der Entitätseigenschaften abrufen. Bei dieser Methode, der so genannten  *projection*, wird die Bandbreite reduziert und die Abfrageleistung gesteigert, vor allem bei großen Entitäten. Um eine Eigenschaft, die abgerufen werden soll, anzugeben, übergeben Sie den Namen der Eigenschaft an die **Query->addSelectField**-Methode. Sie können diese Methode mehrfach aufrufen, um weitere Eigenschaften hinzuzufügen. Nach dem Ausführen von **TableRestProxy->queryEntities** haben die zurückgegebenen Entitäten nur die ausgewählten Eigenschaften. (Wenn Sie eine Teilmenge der Tabellenentitäten zurückgeben möchten, verwenden Sie einen Filter, wie in den Abfragen oben gezeigt wird.)

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
		// http://msdn.microsoft.com/library/azure/dd179438.aspx
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

## Vorgehensweise: Aktualisieren einer Entität

Eine vorhandene Entität kann mithilfe der **Entity->setProperty** und **Entity->addProperty**-Methoden aktualisiert werden, indem anschließend **TableRestProxy->updateEntity** aufgerufen wird. Das folgende Beispiel ruft eine Entität ab, ändert eine Eigenschaft, entfernt eine andere Eigenschaft und fügt eine neue Eigenschaft hinzu. Beachten Sie, dass das Entfernen einer Eigenschaft durch Festlegen ihres Werts auf **null** erfolgt. 

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
		// http://msdn.microsoft.com/library/azure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

## Vorgehensweise: Löschen einer Entität

Um eine Entität zu löschen, übergeben Sie den Tabellennamen und die Werte für  `PartitionKey` und  `RowKey` der Entität an die **TableRestProxy->deleteEntity**-Methode.

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
		// http://msdn.microsoft.com/library/azure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Beachten Sie, dass Sie für Parallelitätsüberprüfungen das Etag für eine zu löschende Entität über die **DeleteEntityOptions->setEtag**-Methode einstellen können, indem Sie das **deleteEntityOptions**-Objekt als vierten Parameter an **deleteEntity** weitergeben.

## Vorgehensweise: Batch-Tabellenvorgänge

Mit der **TableRestProxy->batch**-Methode können Sie mehrere Vorgänge in einer einzelnen Anforderung ausführen. Das Muster umfasst hier das Hinzufügen von Vorgängen zum **BatchRequest**-Objekt und das anschließende Übergeben des **BatchRequest**-Objekts auf die **TableRestProxy->batch**-Methode. Um einen Vorgang zu einem **BatchRequest**-Objekt hinzuzufügen, können Sie eine der folgenden Methoden mehrfach aufrufen:

* **addInsertEntity** (adds an insertEntity operation)
* **addUpdateEntity** (adds an updateEntity operation)
* **addMergeEntity** (adds a mergeEntity operation)
* **addInsertOrReplaceEntity** (adds an insertOrReplaceEntity operation)
* **addInsertOrMergeEntity** (adds an insertOrMergeEntity operation)
* **addDeleteEntity** (adds a deleteEntity operation)

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
		// http://msdn.microsoft.com/library/azure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Weitere Informationen zu Batch-Tabellenvorgängen finden Sie unter [Durchführen von Entitätsgruppentransaktionen][entity-group-transactions].

## Vorgehensweise: Löschen einer Tabelle

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
		// http://msdn.microsoft.com/library/azure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

## Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen des Azure-Tabellenspeicherdiensts vertraut gemacht haben, folgen Sie diesen Links, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.

- Weitere Informationen finden Sie in der MSDN-Referenz: [Azure Storage](http://msdn.microsoft.com/library/azure/gg433040.aspx)
- Besuchen Sie den [Azure Storage-Teamblog](http://blogs.msdn.com/b/windowsazurestorage/).

[Herunterladen]: http://go.microsoft.com/fwlink/?LinkID=252473
[Speichern von und Zugreifen auf Daten in Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[require_once]: http://php.net/require_once
[table-service-timeouts]: http://msdn.microsoft.com/library/azure/dd894042.aspx

[table-data-model]: http://msdn.microsoft.com/library/azure/dd179338.aspx
[filters]: http://msdn.microsoft.com/library/azure/dd894031.aspx
[entity-group-transactions]: http://msdn.microsoft.com/library/azure/dd894038.aspx

<!--HONumber=49--> 