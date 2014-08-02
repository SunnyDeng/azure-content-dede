<properties linkid="develop-php-table-service" urlDisplayName="Table Service" pageTitle="How to use table storage (PHP) | Microsoft Azure" metaKeywords="Azure Table service PHP, Azure creating table, Azure deleting table, Azure insert table, Azure query table" description="Learn how to use the Table service from PHP to create and delete a table, and insert, delete, and query the table." metaCanonical="" services="storage" documentationCenter="PHP" title="How to use the Table service from PHP" authors="" solutions="" manager="" editor="" />

Verwenden des Tabellendiensts von PHP
=====================================

Dieser Leitfaden erläutert Ihnen das Durchführen allgemeiner Szenarios mit dem Azure-Tabellendienst. Die Beispiele wurden in PHP geschrieben und verwenden das [Azure-SDK für PHP](http://go.microsoft.com/fwlink/?LinkID=252473). Die besprochenen Szenarios umfassen **das Erstellen und Löschen einer Tabelle sowie das Einfügen, Löschen und Abfragen von Entitäten in einer Tabelle**. Weitere Informationen zum Azure-Tabellendienst finden Sie im Abschnitt [Nächste Schritte](#NextSteps).

Inhaltsverzeichnis
------------------

-   [Was ist der Tabellendienst?](#what-is)
-   [Konzepte](#concepts)
-   [Erstellen eines Azure-Speicherkontos](#CreateAccount)
-   [Erstellen einer PHP-Anwendung](#CreateApplication)
-   [Konfigurieren der Anwendung für den Zugriff auf den Tabellendienst](#ConfigureStorage)
-   [Einrichten einer Azure-Speicherverbindung](#ConnectionString)
-   [Vorgehensweise: Erstellen einer Tabelle](#CreateTable)
-   [Vorgehensweise: Hinzufügen einer Entität zu einer Tabelle](#AddEntity)
-   [Vorgehensweise: Abrufen einer einzelnen Entität](#RetrieveEntity)
-   [Vorgehensweise: Abrufen aller Entitäten in einer Partition](#RetEntitiesInPartition)
-   [Vorgehensweise: Abrufen einer Teilmenge von Entitäten in einer Partition](#RetrieveSubset)
-   [Vorgehensweise: Abrufen einer Teilmenge von Entitätseigenschaften](#RetPropertiesSubset)
-   [Vorgehensweise: Aktualisieren einer Entität](#UpdateEntity)
-   [Vorgehensweise: Batch-Tabellenvorgänge](#BatchOperations)
-   [Vorgehensweise: Löschen einer Tabelle](#DeleteTable)
-   [Nächste Schritte](#NextSteps)

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

Erstellen eines Azure-Speicherkontos
------------------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Erstellen einer PHP-Anwendung
-----------------------------

Die einzige Voraussetzung für das Erstellen einer PHP-Anwendung, die auf den Azure-Tabellendienst zugreift, ist das Referenzieren von Klassen im Azure-SDK für PHP innerhalb des Codes. Sie können die Anwendung mit beliebigen Entwicklungstools erstellen, unter anderem auch mit Notepad.

In diesem Leitfaden verwenden Sie Tabellendienstfunktionen, die lokal aus einer PHP-Anwendung heraus oder im Code, der in einer Azure-Webrolle, -Workerrolle oder -Website ausgeführt wird, aufgerufen werden.

Abrufen der Azure-Clientbibliotheken
------------------------------------

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

Konfigurieren der Anwendung für den Zugriff auf den Tabellendienst
------------------------------------------------------------------

Um die APIs des Azure-Tabellendiensts zu verwenden, müssen Sie folgendermaßen vorgehen:

1.  Verweisen Sie mithilfe der [require\_once](http://php.net/require_once)-Anweisung auf die Autoloaderdatei und
2.  Verweisen Sie auf alle Klassen, die Sie möglicherweise verwenden.

Das folgende Beispiel zeigt, wie die Autoloaderdatei eingeschlossen und die **ServicesBuilder**-Klasse referenziert wird.

> [WACOM.NOTE] In diesem Beispiel (und in anderen Beispielen in diesem Artikel) wird angenommen, dass Sie die PHP-Clientbibliotheken für Azure über Composer installiert haben. Falls Sie die Bibliotheken manuell oder als PEAR-Paket installiert haben, müssen Sie auf die Autoloaderdatei `WindowsAzure.php` verweisen.

    require_once 'vendor\autoload.php';
    use WindowsAzure\Common\ServicesBuilder;

In den Beispielen weiter unten wird die `require_once`-Anweisung immer angezeigt, aber nur die Klassen, die für die Ausführung des Beispiels erforderlich sind, werden referenziert.

Einrichten einer Azure-Speicherverbindung
-----------------------------------------

Um einen Azure-Tabellendienstclient zu instanzieren, benötigen Sie zunächst eine gültige Verbindungszeichenfolge. Das Format der Tabellendienst-Verbindungszeichenfolge ist folgendermaßen:

Für den Zugriff auf einen Livedienst:

    DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Für den Zugriff auf den Emulatorspeicher:

    UseDevelopmentStorage=true

Um einen Azure-Dienstclient zu erstellen, müssen Sie die **ServicesBuilder**-Klasse verwenden. Sie können:

-   Die Verbindungszeichenfolge direkt an die Klasse weitergeben oder
-   den **CloudConfigurationManager (CCM)** verwenden, um mehrere externe Quellen für die Verbindungszeichenfolge zu überprüfen:
    -   Standardmäßig verfügt sie über Unterstützung für eine externe Quelle – Umgebungsvariablen.
    -   Sie können neue Quellen durch Erweitern der **ConnectionStringSource**-Klasse hinzufügen.

Für die hier erläuterten Beispiele wird die Verbindungszeichenfolge direkt weitergegeben.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;

    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

Vorgehensweise: Erstellen einer Tabelle
---------------------------------------

Mithilfe eines **TableRestProxy**-Objekts können Sie eine Tabelle mit der **createTable**-Methode erstellen. Wenn Sie eine Tabelle erstellen, können Sie das Tabellendiensttimeout festlegen. (Weitere Informationen zum Tabellendiensttimeout finden Sie unter [Festlegen von Timeouts für Tabellendienstvorgänge](http://msdn.microsoft.com/en-us/library/windowsazure/dd894042.aspx).)

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        // Create table.
        $tableRestProxy->createTable("mytable");
    }
    catch(ServiceException $e){
        $code = $e->getCode();
        $error_message = $e->getMessage();
        // Ausnahme basierend auf Fehlercodes und -meldungen behandeln.
        // Fehlercodes und -meldungen sind hier verfügbar: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179438.aspx
    }

Informationen zu Einschränkungen für Tabellennamen finden Sie unter [Grundlegendes zum Tabellendienst-Datenmodell](http://msdn.microsoft.com/en-us/library/windowsazure/dd179338.aspx).

Vorgehensweise: Hinzufügen einer Entität zu einer Tabelle
---------------------------------------------------------

Um einer Tabelle eine Entität hinzuzufügen, erstellen Sie ein neues **Entity**-Objekt und übergeben es an **TableRestProxy-\>insertEntity**. Beachten Sie, dass Sie beim Erstellen einer Entität einen `PartitionKey` und einen `RowKey` angeben müssen. Dabei handelt es sich um eindeutige Kennzeichner für eine Entität und um Werte, die viel schneller als andere Entitätseigenschaften abgefragt werden können. Das System verwendet `PartitionKey`, um die Tabellenentitäten automatisch über viele Speicherknoten zu verteilen. Entitäten mit dem gleichen `PartitionKey` werden auf dem gleichen Knoten gespeichert. (Vorgänge für mehrere Entitäten, die auf dem gleichen Knoten gespeichert sind, haben eine höhere Leistung als Vorgänge für Entitäten, die auf verschiedenen Knoten gespeichert sind.) Der `RowKey` ist die eindeutige ID einer Entität innerhalb einer Partition.

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
        // Ausnahme basierend auf Fehlercodes und -meldungen behandeln.
        // Fehlercodes und -meldungen sind hier verfügbar: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
    }

Informationen zu Tabelleneigenschaften und -typen finden Sie unter [Grundlegendes zum Tabellendienst-Datenmodell](http://msdn.microsoft.com/en-us/library/windowsazure/dd179338.aspx).

Die **TableRestProxy**-Klasse bietet zwei alternative Methoden zum Einfügen von Entitäten: **insertOrMergeEntity** und **insertOrReplaceEntity**. Um diese Methoden zu verwenden, erstellen Sie ein neues **Entity**-Objekt, und übergeben Sie es als Parameter an eine Methode. Jede Methode fügen die Entität ein, falls sie nicht vorhanden ist. Falls die Entität bereits vorhanden ist, aktualisiert **insertOrMergeEntity** die Eigenschaftswerte, falls die Eigenschaften bereits vorhanden sind, und fügt neue Werte hinzu, falls sie nicht vorhanden sind, während **insertOrReplaceEntity** eine vorhandene Entität vollständig ersetzt. Das folgende Beispiel zeigt die Verwendung von **insertOrMergeEntity**. Falls die Entität mit `PartitionKey` "tasksSeattle" und `RowKey` "1" nicht bereits vorhanden ist, wird sie eingefügt. Falls sie jedoch zuvor eingefügt wurde (wie in dem Beispiel oben gezeigt), wird die `DueDate`-Eigenschaft aktualisiert und die `Status`-Eigenschaft hinzugefügt. Auch die Eigenschaften `Description` und `Location` werden aktualisiert, allerdings mit den Werten, mit denen sie effektiv unverändert bleiben. Falls die letzten zwei Eigenschaft nicht wie im Beispiel gezeigt hinzugefügt würden, sondern auf der Zielentität vorhanden wären, blieben ihre vorhandenen Werte unverändert.

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

    try {
        // Calling insertOrReplaceEntity, instead of insertOrMergeEntity as shown,
        // would simply replace the entity with PartitionKey "tasksSeattle" and RowKey "1".
        $tableRestProxy->insertOrMergeEntity("mytable", $entity);
    }
    catch(ServiceException $e){
        // Ausnahme basierend auf Fehlercodes und -meldungen behandeln.
        // Fehlercodes und -meldungen sind hier verfügbar: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Vorgehensweise: Abrufen einer einzelnen Entität
-----------------------------------------------

Mit der **TableRestProxy-\>getEntity**-Methode können Sie eine einzelne Entität durch Abfragen der Werte für `PartitionKey` und `RowKey` abrufen. Im Beispiel unten werden der Partitionsschlüssel `tasksSeattle` und der Zeilenschlüssel `1` an die **getEntity**-Methode übergeben.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        $result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);
    }
    catch(ServiceException $e){
        // Ausnahme basierend auf Fehlercodes und -meldungen behandeln.
        // Fehlercodes und -meldungen sind hier verfügbar: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $entity = $result->getEntity();

    echo $entity->getPartitionKey().":".$entity->getRowKey();

Vorgehensweise: Abrufen aller Entitäten in einer Partition
----------------------------------------------------------

Entitätsabfragen werden mithilfe von Filtern erstellt (weitere Informationen finden Sie unter [Abfragen von Tabellen und Entitäten](http://msdn.microsoft.com/en-us/library/windowsazure/dd894031.aspx)). Um alle Entitäten in einer Partition abzurufen, verwenden Sie den Filter "PartitionKey eq *Partitionsname*". Das folgende Beispiel zeigt, wie alle Entitäten in der Partition `tasksSeattle` durch Übergeben eines Filters an die **queryEntities**-Methode abgerufen werden.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $filter = "PartitionKey eq 'tasksSeattle'";

    try {
        $result = $tableRestProxy->queryEntities("mytable", $filter);
    }
    catch(ServiceException $e){
        // Ausnahme basierend auf Fehlercodes und -meldungen behandeln.
        // Fehlercodes und -meldungen sind hier verfügbar: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $entities = $result->getEntities();

    foreach($entities as $entity){
        echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
    }

Vorgehensweise: Abrufen einer Teilmenge von Entitäten in einer Partition
------------------------------------------------------------------------

Das Muster, das im vorherigen Beispiel verwendet wird, kann auch verwendet werden, um eine Teilmenge der Entitäten in einer Partition abzurufen. Die Teilmenge der Entitäten, die Sie abrufen, wird durch den verwendeten Filter bestimmt (weitere Informationen finden Sie unter [Abfragen von Tabellen und Entitäten](http://msdn.microsoft.com/en-us/library/windowsazure/dd894031.aspx)). Das folgende Beispiel zeigt, wie ein Filter zum Abrufen aller Entitäten mit einem bestimmten Wert für `Location` und einem `DueDate` vor einem angegebenen Datum verwendet wird.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $filter = "Location eq 'Office' and DueDate lt '2012-11-5'";

    try {
        $result = $tableRestProxy->queryEntities("mytable", $filter);
    }
    catch(ServiceException $e){
        // Ausnahme basierend auf Fehlercodes und -meldungen behandeln.
        // Fehlercodes und -meldungen sind hier verfügbar: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $entities = $result->getEntities();

    foreach($entities as $entity){
        echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
    }

Vorgehensweise: Abrufen einer Teilmenge von Entitätseigenschaften
-----------------------------------------------------------------

Eine Abfrage kann eine Teilmenge der Entitätseigenschaften abrufen. Bei dieser Methode, der sogenannten *Projektion*, wird die Bandbreite reduziert und die Abfrageleistung gesteigert, vor allem bei großen Entitäten. Um eine abzurufende Eigenschaft anzugeben, übergeben Sie den Namen der Eigenschaft an die **Query-\>addSelectField**-Methode. Sie können diese Methode mehrfach aufrufen, um weitere Eigenschaften hinzuzufügen. Nach dem Ausführen von **TableRestProxy-\>queryEntities** haben die zurückgegebenen Entitäten nur die ausgewählten Eigenschaften. (Wenn Sie eine Teilmenge der Tabellenentitäten zurückgeben möchten, verwenden Sie einen Filter, wie in den Abfragen oben gezeigt wird.)

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\Table\Models\QueryEntitiesOptions;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $options = new QueryEntitiesOptions();
    $options->addSelectField("Description");

    try {
        $result = $tableRestProxy->queryEntities("mytable", $options);
    }
    catch(ServiceException $e){
        // Ausnahme basierend auf Fehlercodes und -meldungen behandeln.
        // Fehlercodes und -meldungen sind hier verfügbar: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179438.aspx
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

Vorgehensweise: Aktualisieren einer Entität
-------------------------------------------

Eine vorhandene Entität kann mithilfe der Methoden **Entity-\>setProperty** und **Entity-\>addProperty** auf der Entität und dem darauf folgenden Aufruf von **TableRestProxy-\>updateEntity** aktualisiert werden. Das folgende Beispiel ruft eine Entität ab, ändert eine Eigenschaft, entfernt eine andere Eigenschaft und fügt eine neue Eigenschaft hinzu. Beachten Sie, dass das Entfernen einer Eigenschaft durch Festlegen ihres Werts auf **null** erfolgt.

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

    try {
        $tableRestProxy->updateEntity("mytable", $entity);
    }
    catch(ServiceException $e){
        // Ausnahme basierend auf Fehlercodes und -meldungen behandeln.
        // Fehlercodes und -meldungen sind hier verfügbar: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Vorgehensweise: Löschen einer Entität
-------------------------------------

Um eine Entität zu löschen, übergeben Sie den Tabellennamen und die Werte für `PartitionKey` und `RowKey` der Entität an die **TableRestProxy-\>deleteEntity**-Methode.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        // Delete entity.
        $tableRestProxy->deleteEntity("mytable", "tasksSeattle", "2");
    }
    catch(ServiceException $e){
        // Ausnahme basierend auf Fehlercodes und -meldungen behandeln.
        // Fehlercodes und -meldungen sind hier verfügbar: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Beachten Sie Folgendes für Parallelitätsüberprüfungen: Sie können den ETag für eine zu löschende Entität mithilfe der **DeleteEntityOptions-\>setEtag**-Methode und durch Übergeben des **DeleteEntityOptions**-Objekts an **deleteEntity** als vierten Parameter festlegen.

Vorgehensweise: Batch-Tabellenvorgänge
--------------------------------------

Mithilfe der **TableRestProxy-\>batch**-Methode können Sie mehrere Vorgänge in einer einzelnen Anforderung ausführen. Das Muster umfasst hier das Hinzufügen von Vorgängen zum **BatchRequest**-Objekt und die darauf folgende Übergabe des **BatchRequest**-Objekts an die **TableRestProxy-\>batch**-Methode. Um einem **BatchRequest**-Objekt einen Vorgang hinzuzufügen, können Sie eine der folgenden Methoden mehrfach aufrufen:

-   **addInsertEntity** (fügt einen insertEntity-Vorgang hinzu)
-   **addUpdateEntity** (fügt einen updateEntity-Vorgang hinzu)
-   **addMergeEntity** (fügt einen mergeEntity-Vorgang hinzu)
-   **addInsertOrReplaceEntity** (fügt einen insertOrReplaceEntity-Vorgang hinzu)
-   **addInsertOrMergeEntity** (fügt einen insertOrMergeEntity-Vorgang hinzu)
-   **addDeleteEntity** (fügt einen deleteEntity-Vorgang hinzu)

Das folgende Beispiel zeigt die Ausführung von **insertEntity**- und **deleteEntity**-Vorgängen in einer einzelnen Anforderung:

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

    try {
        $tableRestProxy->batch($operations);
    }
    catch(ServiceException $e){
        // Ausnahme basierend auf Fehlercodes und -meldungen behandeln.
        // Fehlercodes und -meldungen sind hier verfügbar: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Weitere Informationen zu Batch-Tabellenvorgängen finden Sie unter [Durchführen von Entitätsgruppentransaktionen](http://msdn.microsoft.com/en-us/library/windowsazure/dd894038.aspx).

Vorgehensweise: Löschen einer Tabelle
-------------------------------------

Um eine Tabelle zu löschen, übergeben Sie den Tabellennamen an die **TableRestProxy-\>deleteTable**-Methode.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        // Delete table.
        $tableRestProxy->deleteTable("mytable");
    }
    catch(ServiceException $e){
        // Ausnahme basierend auf Fehlercodes und -meldungen behandeln.
        // Fehlercodes und -meldungen sind hier verfügbar: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Nächste Schritte
----------------

Da Sie jetzt die Grundlagen des Azure-Tabellendienstes erlernt haben, folgen Sie diesen Links, um Informationen zum Ausführen komplexerer Speicheraufgaben zu erhalten.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Speichern und Zugreifen auf Daten in Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx)
-   Besuchen Sie den Blog des Azure-Speicherteams: &lt;http://blogs.msdn.com/b/windowsazurestorage/\&gt;

