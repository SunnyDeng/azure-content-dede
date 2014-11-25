<properties linkid="dev-nodejs-how-to-table-services" urlDisplayName="Table Service" pageTitle="How to use table storage (Node.js) | Microsoft Azure" metaKeywords="Azure table storage service, Azure table service Node.js, table storage Node.js" description="Learn how to use the table storage service in Azure. Code samples are written using the Node.js API." metaCanonical="" services="storage" documentationCenter="Node.js" title="How to Use the Table Service from Node.js" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="01/01/1900" ms.author="larryfr" />

# Verwenden des Tabellenspeicherdiensts aus Node.js

In diesem Leitfaden wird die Durchführung häufiger Szenarien mit dem Window
Azure-Tabellenspeicherdienst demonstriert. Die Beispiele wurden unter Verwendung der
Node.js-API geschrieben. Die behandelten Szenarien umfassen das **Erstellen und Löschen
einer Tabelle sowie das Einfügen und Abfragen von Tabellenentitäten**. Weitere
Informationen zu Tabellen finden Sie im Abschnitt [Nächste Schritte][Nächste Schritte].

## Inhaltsverzeichnis

-   [Was ist der Tabellenspeicherdienst?][Was ist der Tabellenspeicherdienst?]
-   [Konzepte][Konzepte]
-   [Erstellen eines Azure-Speicherkontos][Erstellen eines Azure-Speicherkontos]
-   [Erstellen einer Node.js-Anwendung][Erstellen einer Node.js-Anwendung]
-   [Konfigurieren der Anwendung für den Speicherzugriff][Konfigurieren der Anwendung für den Speicherzugriff]
-   [Einrichten einer Azure-Speicherverbindung][Einrichten einer Azure-Speicherverbindung]
-   [Gewusst wie: Erstellen einer Tabelle][Gewusst wie: Erstellen einer Tabelle]
-   [Gewusst wie: Hinzufügen einer Entität zu einer Tabelle][Gewusst wie: Hinzufügen einer Entität zu einer Tabelle]
-   [Gewusst wie: Aktualisieren einer Entität][Gewusst wie: Aktualisieren einer Entität]
-   [Gewusst wie: Arbeiten mit Gruppen von Entitäten][Gewusst wie: Arbeiten mit Gruppen von Entitäten]
-   [Gewusst wie: Abrufen einer Entität][Gewusst wie: Abrufen einer Entität]
-   [Gewusst wie: Abfragen einer Gruppe von Entitäten][Gewusst wie: Abfragen einer Gruppe von Entitäten]
-   [Gewusst wie: Löschen einer Entität][Gewusst wie: Löschen einer Entität]
-   [Gewusst wie: Löschen einer Tabelle][Gewusst wie: Löschen einer Tabelle]
-   [Gewusst wie: Arbeiten mit Shared Access Signatures][Gewusst wie: Arbeiten mit Shared Access Signatures]
-   [Nächste Schritte][Nächste Schritte]

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

## <a name="create-account"></a>Erstellen eines Azure-Speicherkontos

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="create-app"> </a>Erstellen einer Node.js-Anwendung

Erstellen Sie eine leere Node.js-Anwendung. Hinweise zum Erstellen von Node.js-Anwendungen finden Sie unter [Erstellen und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website][Erstellen und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website], [Node.js-Clouddienst][Node.js-Clouddienst] (mithilfe von Windows PowerShell) oder [Website mit WebMatrix][Website mit WebMatrix].

## <a name="configure-access"> </a>Konfigurieren der Anwendung für den Speicherzugriff

Um Azure-Speicher verwenden zu können, müssen Sie das Azure Storage SDK für Node.js herunterladen. Es enthält eine Reihe von Bibliotheken,
die mit den REST-Speicherdiensten kommunizieren.

### Verwenden von Node-Paket-Manager (NPM) zum Beziehen des Pakets

1.  Verwenden Sie eine Befehlszeilenschnittstelle, z. B. **PowerShell** (Windows,) **Terminal** (Mac) oder **Bash** (Unix), und navigieren Sie zu dem Ordner, in dem Sie die Beispielanwendung erstellt haben.

2.  Geben Sie **npm install azure-storage** in das Befehlsfenster ein. Die Ausgabe des Befehls sollte wie folgt aussehen:

        azure-storage@0.1.0 node_modules\azure-storage
        ├── extend@1.2.1
        ├── xmlbuilder@0.4.3
        ├── mime@1.2.11
        ├── underscore@1.4.4
        ├── validator@3.1.0
        ├── node-uuid@1.4.1
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)

3.  Sie können den Befehl **ls** manuell aufrufen, um sich davon zu überzeugen, dass der Ordner
    **node\_modules** erstellt wurde. Dieser Ordner enthält
    das **azure-storage**-Paket mit den Bibliotheken, die Sie für den
    Speicherzugriff benötigen.

### Importieren des Pakets

Verwenden Sie Editor oder einen anderen Texteditor, um die folgende Zeile am Anfang der Datei
**server.js** der Anwendung einzufügen, in der Sie den Speicher nutzen möchten:

           var azure = require('azure-storage');

## <a name="setup-connection-string"> </a>Einrichten einer Azure-Speicherverbindung

Das Azure-Modul entnimmt den Umgebungsvariablen AZURE\_STORAGE\_ACCOUNT und AZURE\_STORAGE\_ACCESS\_KEY oder AZURE\_STORAGE\_CONNECTION\_STRING die Informationen, die zum Herstellen einer Verbindung mit Ihrem Azure-Speicherkonto benötigt werden. Wenn diese Umgebungsvariablen nicht festgelegt wurden, müssen Sie die Kontoinformationen beim Aufruf von **TableService** angeben.

Ein Beispiel zum Festlegen der Umgebungsvariablen im Verwaltungsportal für eine Azure-Website finden Sie unter [Node.js-Webanwendung mit Speicher][Node.js-Webanwendung mit Speicher].

## <a name="create-table"> </a>Erstellen einer Tabelle

Mit dem folgenden Code wird ein **TableService**-Objekt erstellt und zum
Erstellen einer neuen Tabelle verwendet. Fügen Sie Folgenden am Anfang der Datei **server.js** ein.

        var tableSvc = azure.createTableService();

Durch den Aufruf von **createTableIfNotExists** wird eine neue Tabelle mit dem angegebenen
Namen erstellt, sofern sie nicht vorhanden ist. Im folgenden Beispiel wird eine neue Tabelle namens 'mytable' erstellt, wenn diese noch nicht vorhanden ist:

    tableSvc.createTableIfNotExists('mytable', function(error, result, response){
        if(!error){
            // Table exists or created
        }
    });

Das `result` ist `true`, wenn eine neue Tabelle erstellt wird, und `false`, wenn die Tabelle bereits vorhanden ist. `response` enthält Informationen zu der Anforderung.

### Filter

Auf die Vorgänge, die mit **TableService** ausgeführt werden, können optionale Filtervorgänge angewendet werden. Filtervorgänge können Protokollierung, automatische Wiederholung usw. umfassen. Filter sind Objekte, die eine Methode mit einer Signatur implementieren:

          function handle (requestOptions, next)

Nachdem die Vorverarbeitung der Anforderungsoptionen angeschlossen ist, muss die Methode "next" aufrufen und hierbei eine Rückruffunktion mit der folgenden Signatur übergeben:

        function (returnObject, finalCallback, next)

Nachdem das returnObject-Objekt (die Antwort auf die an den Server gesendete Anforderung) verarbeitet wurde, muss in dieser Rückruffunktion entweder "next" aufgerufen werden, wenn die Tabelle vorhanden ist, um weitere Filter zu verarbeiten, oder andernfalls einfach "finallCallback" aufrufen, um den Dienstaufruf zu beenden.

Zwei Filter, die eine Wiederholungslogik implementieren, sind im Azure SDK für Node.js enthalten: **ExponentialRetryPolicyFilter** und **LinearRetryPolicyFilter**. Mit folgendem Code wird ein **TableService**-Objekt, das **ExponentialRetryPolicyFilter** verwendet:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var tableSvc = azure.createTableService().withFilter(retryOperations);

## <a name="add-entity"> </a>Hinzufügen einer Entität zu einer Tabelle

Um eine Entität hinzuzufügen, erstellen Sie zunächst ein Objekt,
das die Entitätseigenschaften definiert. Alle Entitäten müssen **PartitionKey** und **RowKey** enthalten, die eindeutige Bezeichner für die Entität darstellen.

-   **PartitionKey**: Bestimmt die Partition, in der die Entität gespeichert ist.

-   **RowKey**: Identifiziert die Entität innerhalb der Partition eindeutig.

**PartitionKey** und **RowKey** müssen Zeichenfolgenwerte sein. Weitere Informationen finden Sie unter [Grundlegendes zum Tabellendienst-Datenmodell][Grundlegendes zum Tabellendienst-Datenmodell].

Nachfolgend sehen Sie ein Beispiel für die Definition einer Entität. Beachten Sie, dass **dueDate** vom Typ **Edm.DateTime** definiert ist. Die Angabe des Typs ist optional. Nicht angegebene Typen werden abgeleitet.

    var task = { 
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '1'},
      description: {'_':'take out the trash'},
      dueDate: {'_':new Date(2015, 6, 20), '$':'Edm.DateTime'}
    };

> [WACOM.NOTE] Für jeden Datensatz gibt es ein **Timestamp**-Feld, das von Azure festgelegt wird, wenn eine Entität eingefügt oder aktualisiert wird.

Sie können Entitäten auch mit dem **entityGenerator** erstellen. Im folgenden Beispiel wird dieselbe Task-Entität mit dem **entityGenerator** erstellt.

    var entGen = azure.TableUtilities.entityGenerator;
    var task = {
      PartitionKey: entGen.String('hometasks'),
      RowKey: entGen.String('1'),
      description: entGen.String('take out the trash'),
      dueDate: entGen.DateTime(new Date(Date.UTC(2015, 6, 20))),
    };

Um der Tabelle eine Entität hinzuzufügen, übergeben Sie
das Entitätsobjekt an die **insertEntity**-Methode.

    tableSvc.insertEntity('mytable',task, function (error, result, response) {
        if(!error){
            // Entity inserted
        }
    });

Ist der Vorgang erfolgreich, enthält `result` das [ETag][ETag] des eingefügten Datensatzes, und `response` enthält Informationen zu dem Vorgang.

> [WACOM.NOTE] Standardmäßig gibt **insertEntity** die eingefügte Entität nicht im Rahmen der `response`-Informationen zurück. Wenn Sie weitere Vorgänge mit der Entität ausführen oder die Informationen zwischenspeichern möchten, können Sie sie als Teil von `result` zurückgeben. Hierzu aktivieren Sie **echoContent** wie folgt:
>
> `tableSvc.insertEntity('mytable', task, {echoContent: true}, function (error, result, response) {...}`

## <a name="update-entity"> </a>Aktualisieren einer Entität

Es sind mehrere Methoden zum Aktualisieren einer vorhandenen Entität vorhanden:

-   **updateEntity**: Aktualisiert eine vorhandene Entität, indem sie ersetzt wird.

-   **mergeEntity**: Aktualisiert eine vorhandene Entität durch Zusammenführen neuer Eigenschaftenwerte mit der vorhandenen Entität.

-   **insertOrReplaceEntity**: Aktualisiert eine vorhandene Entität, indem sie ersetzt wird. Wenn keine Entität vorhanden ist, wird eine neue eingefügt.

-   **insertOrMergeEntity**: Aktualisiert eine vorhandene Entität durch Zusammenführen neuer Eigenschaftenwerte mit der vorhandenen Entität. Wenn keine Entität vorhanden ist, wird eine neue eingefügt.

Das folgende Beispiel zeigt, wie eine Entität mit **updateEntity** aktualisiert wird:

    tableSvc.updateEntity('mytable', updatedTask, function(error, result, response){
      if(!error) {
        // Entity updated
      }
    });

> [WACOM.NOTE] Standardmäßig wird beim Aktualisieren einer Entität nicht überprüft, ob die aktualisierten Daten zuvor von einem anderen Prozess geändert wurden. Um gleichzeitige Aktualisierungen zu unterstützen, gehen Sie wie folgt vor:
>
> 1.  Rufen Sie das ETag des aktualisierten Objekts ab. Es wird im Rahmen der `response` für jeden entitätsbezogenen Vorgang zurückgegeben und kann durch `response['.metadata'].etag` abgerufen werden.
>
> 2.  Wenn Sie einen Aktualisierungsvorgang für eine Entität ausführen, sollten Sie der neuen Entität die zuvor abgerufenen ETag-Informationen hinzufügen. Beispiel:
>
>     `entity2['.metadata'].etag = currentEtag;`
>
> 3.  Führen Sie den Aktualisierungsvorgang aus. Wurde die Entität seit dem Abruf des ETag-Werts beispielsweise durch eine andere Instanz Ihrer Anwendung geändert, wird ein `error` zurückgegeben, der besagt, dass die in der Anforderung angegebene Aktualisierungsbedingung nicht erfüllt ist.
>
Bei **updateEntity** und **mergeEntity** schlägt der Aktualisierungsvorgang fehl, wenn die zu aktualisierende Entität nicht existiert. Daher sollten Sie **insertOrReplaceEntity** oder **insertOrMergeEntity** verwenden, wenn Sie eine Entität unabhängig davon speichern möchten, ob sie bereits vorhanden ist.

Für erfolgreiche Aktualisierungsvorgänge enthält das `result` das **ETag** der aktualisierten Entität.

## <a name="change-entities"> </a>Arbeiten mit Gruppen von Entitäten

Gelegentlich ist es sinnvoll, mehrere Vorgänge zusammen in einem Stapel
zu senden, um die atomische Verarbeitung durch den Server sicherzustellen. Dazu erstellen Sie
mit der **TableBatch**-Klasse einen Stapel und führen dann mit der **executeBatch**-Methode von **TableService** die gestapelten Vorgänge aus.

Im folgenden Beispiel wird gezeigt, wie zwei Entitäten in einem Stapel übermittelt werden:

    var task1 = { 
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '1'},
      description: {'_':'Take out the trash'},
      dueDate: {'_':new Date(2015, 6, 20)}
    };
    var task2 = { 
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '2'},
      description: {'_':'Wash the dishes'},
      dueDate: {'_':new Date(2015, 6, 20)}
    };

    var batch = new azure.TableBatch();

    batch.insertEntity(task1, {echoContent: true});
    batch.insertEntity(task2, {echoContent: true});

    tableSvc.executeBatch('mytable', batch, function (error, result, response) {
      if(!error) {
        // Batch completed
      }
    });

Bei erfolgreichen Stapelvorgängen enthält `result` Informationen für jeden Vorgang im Stapel.

### Arbeiten mit gestapelten Vorgängen

Die einem Stapel hinzugefügten Vorgänge finden Sie in der `operations`-Eigenschaft. Sie können auch die folgenden Methoden verwenden, um mit Vorgängen zu arbeiten.

-   **clear**: löscht alle Vorgänge aus einem Stapel.

-   **getOperations**: ruft einen Vorgang aus dem Stapel ab.

-   **hasOperations**: gibt true zurück, wenn der Stapel Vorgänge enthält.

-   **removeOperations**: entfernt einen Vorgang.

-   **size**: gibt die Anzahl von Vorgängen im Stapel zurück.

## <a name="query-for-entity"> </a>Abrufen einer Entität

Wenn Sie eine bestimmte Entität basierend auf **PartitionKey** und **RowKey** zurückgeben möchten, verwenden Sie die **retrieveEntity**-Methode.

    tableSvc.retrieveEntity('mytable', 'hometasks', '1', function(error, result, response){
      if(!error){
        // result contains the entity
      }
    });

Nach Abschluss des Vorgangs enthält `result` die Entität.

## <a name="query-set-entities"> </a>Abfragen einer Gruppe von Entitäten

Um eine Tabelle abzufragen, erstellen Sie mithilfe des **TableQuery**-Objekts
einen Abfrageausdruck mit folgenden Klauseln:

-   **select**: Die zurückzugebenden Felder aus der Abfrage.

-   **where**: Die where-Klausel.

    -   **and**: Eine `and` where-Bedingung.

    -   **or**: Eine `or` where-Bedingung.

-   **top**: Die Anzahl der abzurufenden Elemente.

Im folgenden Beispiel wird eine Abfrage erstellt, die die ersten 5 Elemente mit dem PartitionKey 'hometasks' zurückgibt.

    var query = new azure.TableQuery()
      .top(5)
      .where('PartitionKey eq ?', 'hometasks');

Da **select** nicht verwendet wird, werden alle Felder zurückgegeben. Verwenden Sie **queryEntities**, um die Abfrage für eine Tabelle auszuführen. Im folgenden Beispiel werden mit der Abfrage Entitäten aus 'mytable' zurückgegeben.

    tableSvc.queryEntities('mytable',query, null, function(error, result, response) {
      if(!error) {
        // query was successful
      }
    });

Nach erfolgreicher Ausführung enthält `result.entries` ein Array von Entitäten, die die Abfrage erfüllen. Konnten nicht alle Entitäten von der Abfrage zurückgegeben werden, kann `result.continuationToken` als dritter Parameter von **queryEntities** verwendet werden, um weitere Ergebnisse abzurufen. In der ersten Abfrage sollte der zweite Parameter *null* sein.

### Abfragen einer Teilmenge von Entitätseigenschaften

Mit einer Abfrage einer Tabelle können nur einige wenige Felder einer Entität abgerufen werden.
Dies reduziert die Bandbreite und kann die Abfrageleistung, besonders für große Entitäten, verbessern. Verwenden Sie die **select**-Klausel, und übergeben Sie die Namen der zurückzugebenden Felder. Die folgende Abfrage gibt beispielsweise nur die Felder **description** und **dueDate** zurück.

    var query = new azure.TableQuery()
      .select(['description', 'dueDate'])
      .top(5)
      .where('PartitionKey eq ?', 'hometasks');

## <a name="delete-entity"> </a>Löschen einer Entität

Sie können eine Entität unter Verwendung ihres Partitions- und Zeilenschlüssels löschen. In diesem
Beispiel enthält das Objekt **task1**die Werte **RowKey** und
**PartitionKey** der zu löschenden Entität. Dann wird das Objekt
an die **deleteEntity**-Methode übergeben.

    var task = { 
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '1'}
    };

    tableSvc.deleteEntity('mytable', task, function(error, response){
      if(!error) {
        // Entity deleted
      }
    });

> [WACOM.NOTE] Es ist ratsam, beim Löschen von Elementen ETags zu verwenden, um sicherzustellen, dass das Element nicht von einem anderen Prozess geändert wurde. Weitere Informationen zum Verwenden von ETags finden Sie unter [Gewusst wie: Aktualisieren einer Entität][Gewusst wie: Aktualisieren einer Entität].

## <a name="delete-table"> </a>Löschen einer Tabelle

Mit dem folgenden Code wird eine Tabelle aus einem Speicherkonto gelöscht.

    tableSvc.deleteTable('mytable', function(error, response){
        if(!error){
            // Table deleted
        }
    });

Wenn Sie nicht wissen, ob die Tabelle vorhanden ist, verwenden Sie **deleteTableIfExists**.

## <a name="sas"></a>Gewusst wie: Arbeiten mit Shared Access Signatures

Shared Access Signatures (SAS) ermöglichen auf sichere Art und Weise differenzierten Zugriff auf Tabellen, ohne Speicherkontonamen oder -schlüssel anzugeben. SAS werden häufig verwendet, um eingeschränkten Zugriff auf Ihre Daten zu bieten, beispielsweise um einer mobilen App die Abfrage von Datensätzen zu ermöglichen.

Eine vertrauenswürdige Anwendung, z. B. ein cloudbasierter Dienst, generiert mit der **generateSharedAccessSignature**-Methode des **TableService**-Objekts eine SAS und stellt sie für eine nicht vertrauenswürdige oder halb vertrauenswürdige Anwendung bereit. Zum Beispiel für eine mobile App. Die SAS wird mithilfe einer Richtlinie generiert, die das Anfangs- und das Enddatum der Gültigkeit der SAS sowie die Zugriffsstufe definiert, die dem Inhaber der SAS gewährt wird.

Im folgenden Beispiel wird eine neue Richtlinie für den freigegebenen Zugriff generiert, die dem SAS-Inhaber erlaubt, die Tabelle abzufragen ('r'), und 100 Minuten nach ihrer Erstellung abläuft.

    var startDate = new Date();
    var expiryDate = new Date(startDate);
    expiryDate.setMinutes(startDate.getMinutes() + 100);
    startDate.setMinutes(startDate.getMinutes() - 100);
        
    var sharedAccessPolicy = {
      AccessPolicy: {
        Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
        Start: startDate,
        Expiry: expiryDate
      },
    };

    var tableSAS = tableSvc.generateSharedAccessSignature('mytable', sharedAccessPolicy);
    var host = tableSvc.host;

Beachten Sie, dass die Hostinformationen ebenfalls angegeben werden müssen, da sie erforderlich sind, wenn der SAS-Inhaber versucht auf die Tabelle zuzugreifen.

Die Clientanwendung verwendet die SAS dann zusammen mit **TableServiceWithSAS**, um Vorgänge für die Tabelle auszuführen. Im folgenden Beispiel wird eine Verbindung mit der Tabelle hergestellt und eine Abfrage ausgeführt.

    var sharedTableService = azure.createTableServiceWithSas(host, tableSAS);
    var query = azure.TableQuery()
      .where('PartitionKey eq ?', 'hometasks');
        
    sharedTableService.queryEntities(query, null, function(error, result, response) {
      if(!error) {
        // result contains the entities
      }
    });

Da die SAS nur mit Abfragezugriff generiert wurde, wird beim Versuch, Entitäten einzufügen, zu aktualisieren oder zu löschen, ein Fehler zurückgegeben.

### Zugriffssteuerungslisten

Sie können auch eine Zugriffssteuerungsliste (Access Control List, ACL) verwenden, um die Zugriffsrichtlinie für eine SAS festzulegen. Dies ist nützlich, wenn Sie mehreren Clients Zugriff auf die Tabelle gewähren, aber für jeden Client andere Zugriffsrichtlinien angeben möchten.

Eine ACL wird in einem Array von Zugriffsrichtlinien implementiert, wobei jeder Richtlinie eine ID zugeordnet wird. Im folgenden Beispiel werden zwei Richtlinien definiert, eine für 'user1' und eine für 'user2':

    var sharedAccessPolicy = [
      {
        AccessPolicy: {
          Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
          Start: startDate,
          Expiry: expiryDate
        },
        Id: 'user1'
      },
      {
        AccessPolicy: {
          Permissions: azure.TableUtilities.SharedAccessPermissions.ADD,
          Start: startDate,
          Expiry: expiryDate
        },
        Id: 'user2'
      }
    ];

Im folgenden Beispiel wird zunächst die aktuelle ACL für die Tabelle **hometasks** abgerufen. Anschließend werden die neuen Richtlinien mit **setTableAcl** hinzugefügt. Dieser Ansatz ermöglicht Folgendes:

    tableSvc.getTableAcl('hometasks', function(error, result, response) {
      if(!error){
        //push the new policy into signedIdentifiers
        result.signedIdentifiers.push(sharedAccessPolicy);
        tableSvc.setTableAcl('hometasks', result, function(error, result, response){
          if(!error){
            // ACL set
          }
        });
      }
    });

Nachdem die ACL festgelegt wurde, können Sie basierend auf der ID für eine Richtlinie eine SAS erstellen. Im folgenden Beispiel wird eine neue SAS für 'user2' erstellt:

    tableSAS = tableSvc.generateSharedAccessSignature('hometasks', { Id: 'user2' });

## <a name="next-steps"> </a>Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen der Tabellenspeicherung vertraut gemacht haben,
folgen Sie diesen Links, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Speichern und Zugreifen auf Daten in Azure][Speichern und Zugreifen auf Daten in Azure].
-   [Besuchen Sie den Blog des Azure-Speicherteams][Besuchen Sie den Blog des Azure-Speicherteams].
-   Besuchen Sie das [Azure Storage SDK für Node][Azure Storage SDK für Node]-Repository auf GitHub.

  [Nächste Schritte]: #next-steps
  [Konzepte]: #concepts
  [Erstellen eines Azure-Speicherkontos]: #create-account
  [Erstellen einer Node.js-Anwendung]: #create-app
  [Konfigurieren der Anwendung für den Speicherzugriff]: #configure-access
  [Einrichten einer Azure-Speicherverbindung]: #setup-connection-string
  [Gewusst wie: Erstellen einer Tabelle]: #create-table
  [Gewusst wie: Hinzufügen einer Entität zu einer Tabelle]: #add-entity
  [Gewusst wie: Aktualisieren einer Entität]: #update-entity
  [Gewusst wie: Arbeiten mit Gruppen von Entitäten]: #change-entities
  [Gewusst wie: Abrufen einer Entität]: #query-for-entity
  [Gewusst wie: Abfragen einer Gruppe von Entitäten]: #query-set-entities
  [Gewusst wie: Löschen einer Entität]: #delete-entity
  [Gewusst wie: Löschen einer Tabelle]: #delete-table
  [Gewusst wie: Arbeiten mit Shared Access Signatures]: #sas
  [Erstellen und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website]: /de-de/documentation/articles/web-sites-nodejs-develop-deploy-mac/
  [Node.js-Clouddienst]: /de-de/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Website mit WebMatrix]: /de-de/documentation/articles/web-sites-nodejs-use-webmatrix/
  [Node.js-Webanwendung mit Speicher]: /de-de/documentation/articles/storage-nodejs-use-table-storage-web-site/
  [Grundlegendes zum Tabellendienst-Datenmodell]: http://msdn.microsoft.com/library/azure/dd179338.aspx
  [ETag]: http://en.wikipedia.org/wiki/HTTP_ETag
  [Speichern und Zugreifen auf Daten in Azure]: http://msdn.microsoft.com/de-de/library/windowsazure/gg433040.aspx
  [Besuchen Sie den Blog des Azure-Speicherteams]: http://blogs.msdn.com/b/windowsazurestorage/
  [Azure Storage SDK für Node]: https://github.com/Azure/azure-storage-node
