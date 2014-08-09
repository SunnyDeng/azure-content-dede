<properties linkid="dev-nodejs-how-to-table-services" urlDisplayName="Table Service" pageTitle="How to use table storage (Node.js) | Microsoft Azure" metaKeywords="Azure table storage service, Azure table service Node.js, table storage Node.js" description="Learn how to use the table storage service in Azure. Code samples are written using the Node.js API." metaCanonical="" services="storage" documentationCenter="Node.js" title="How to Use the Table Service from Node.js" authors="" solutions="" manager="" editor="" />

Verwenden des Tabellenspeicherdiensts aus Node.js
=================================================

In diesem Leitfaden wird die Durchführung häufiger Szenarien mit dem Windows Azure-Tabellenspeicherdienst demonstriert. Die Beispiele wurden unter Verwendung der Node.js-API geschrieben. Behandelt werden unter anderem die Szenarien **Erstellen und Löschen von Tabellen, Einfügen und Abfragen von Entitäten in Tabellen**. Weitere Informationen zu Tabellen finden Sie im Abschnitt [Nächste Schritte](#next-steps).

Inhaltsverzeichnis
------------------

-   [Was ist der Tabellenspeicherdienst?](#what-is)
-   [Konzepte](#concepts)
-   [Erstellen eines Azure-Speicherkontos](#create-account)
-   [Erstellen einer Node.js-Anwendung](#create-app)
-   [Konfigurieren der Anwendung für den Speicherzugriff](#configure-access)
-   [Einrichten einer Azure-Speicherverbindung](#setup-connection-string)
-   [Vorgehensweise: Erstellen einer Tabelle](#create-table)
-   [Vorgehensweise: Hinzufügen einer Entität zu einer Tabelle](#add-entity)
-   [Vorgehensweise: Aktualisieren einer Entität](#update-entity)
-   [Vorgehensweise: Arbeiten mit Gruppen von Entitäten](#change-entities)
-   [Vorgehensweise: Abfragen einer Entität](#query-for-entity)
-   [Vorgehensweise: Abfragen einer Gruppe von Entitäten](#query-set-entities)
-   [Vorgehensweise: Abfragen einer Teilmenge von Entitäteneigenschaften](#query-entity-properties)
-   [Vorgehensweise: Löschen einer Entität](#delete-entity)
-   [Vorgehensweise: Löschen einer Tabelle](#delete-table)
-   [Nächste Schritte](#next-steps)

Was ist der Tabellenspeicherdienst?
-----------------------------------

Der Azure Tabellenspeicherdienst erlaubt die Speicherung großer Mengen strukturierter Daten. Der Dienst nimmt authentifizierter Anrufe von innerhalb und außerhalb der Azure-Cloud an. Azure-Tabellen eignen sich hervorragend zur Speicherung strukturierter nicht relationaler Daten. Tabellenspeicherdienste werden hauptsächlich für folgende Zwecke verwendet:

-   Speichern einer riesigen Menge strukturierter Daten (viele TB), die automatisch skaliert werden, um Durchsatzanforderungen zu erfüllen
-   Speicherung von Datensätzen, die keine komplexen Verknüpfungen, Fremdschlüssel oder gespeicherte Prozeduren erfordern und für schnellen Zugriff denormalisiert werden können
-   Schnelles Abfragen von Daten, z. B. Benutzerprofile, mithilfe eines gruppierten Index

Sie können den Tabellenspeicherdienst verwenden, um sehr große Mengen strukturierter nicht relationaler Daten zu speichern und abzufragen, und die Tabellen werden entsprechend der steigenden Datenmenge skaliert.

Konzepte
--------

Der Tabellenspeicherdienst umfasst die folgenden Komponenten:

![Table1](./media/storage-nodejs-how-to-use-table-storage/table1.png)

-   **URL-Format:** Der Code adressiert Tabellen in einem Konto mithilfe dieses Adressformats:

        http://storageaccount.table.core.windows.net/table  

    Über diese Adresse können Azure-Tabellen direkt mit dem OData-Protokoll adressiert werden. Weitere Informationen finden Sie unter [OData.org](http://www.odata.org/)

-   **Speicherkonto:** Der Zugriff auf Azure-Speicher erfolgt immer über ein Speicherkonto. Ein Speicherkonto kann bis zu 100 TB Blob-, Warteschlangen- und Tabellendaten enthalten.

-   **Tabelle**: Eine Tabelle ist eine unbegrenzte Sammlung von Entitäten. Tabellen erzwingen kein Schema für Entitäten. Das bedeutet, dass eine einzelne Tabelle Entitäten mit verschiedenen Eigenschaftensätzen enthalten kann. Ein Konto kann viele Tabellen enthalten.

-   **Entität**: Eine Entität ist ein Satz von Eigenschaften, der einer Datenbankzeile ähnelt Eine Entität kann bis zu 1 MB groß sein.

-   **Eigenschaften**: Eine Eigenschaft ist ein Name-Wert-Paar. Jede Entität kann bis zu 252 Eigenschaften zur Datenspeicherung enthalten. Jede Entität weist außerdem 3 Systemeigenschaften auf, die einen Partitionsschlüssel, einen Zeilenschlüssel und einen Zeitstempel definieren. Entitäten mit demselben Partitionsschlüssel können schneller abgefragt und in atomischen Vorgängen eingesetzt/aktualisiert werden. Der Zeilenschlüssel einer Entität ist ihr eindeutiger Bezeichner innerhalb einer Partition.

Erstellen eines Azure-Speicherkontos
------------------------------------

Für Speichervorgänge benötigen Sie ein Windows Azure-Speicherkonto. Mit den folgenden Schritten können Sie ein Speicherkonto erstellen. (Sie können ein Speicherkonto auch [mithilfe der REST-API](http://msdn.microsoft.com/de-de/library/windowsazure/hh264518.aspx) erstellen.)

1.  Melden Sie sich beim [Windows Azure-Verwaltungsportal](http://manage.windowsazure.com) an.

2.  Klicken Sie unten im Navigationsbereich auf **+NEW**.

    ![+Neu](./media/storage-nodejs-how-to-use-table-storage/plus-new.png)

3.  Klicken Sie auf **Speicherkonto** und anschließend auf **Schnellerfassung**.

    ![Dialogfeld "Schnellerfassung"](./media/storage-nodejs-how-to-use-table-storage/quick-storage.png)

4.  Geben Sie im Feld "URL" einen Unterdomänennamen ein, der im URI für das Speicherkonto verwendet werden soll. Der Eintrag kann drei bis 24 Kleinbuchstaben und Zahlen enthalten. Dieser Wert wird der Hostname im URI, der zum Adressieren von Blob-, Warteschlangen- oder Tabellenspeicherressourcen für das Abonnement verwendet wird.

5.  Wählen Sie eine Region/Affinitätsgruppe, in der sich der Speicher befinden soll. Wenn Sie Speicher aus Ihrer Windows Azure-Anwendung verwenden, wählen Sie die Region aus, in der Sie auch Ihre Anwendung bereitstellen.

6.  Klicken Sie auf **Speicherkonto erstellen**.

Erstellen einer Node.js-Anwendung
---------------------------------

Erstellen Sie eine leere Node.js-Anwendung. Hinweise zum Erstellen von Node.js-Anwendungen finden Sie unter [Erstellen und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website](/de-de/develop/nodejs/tutorials/create-a-website-(mac)/), [Node.js Cloud Service]({localLink:2221} "Web App mit Express") (mit der Windows PowerShell) oder [Website mit WebMatrix](/de-de/develop/nodejs/tutorials/web-site-with-webmatrix/).

Konfigurieren der Anwendung für den Speicherzugriff
---------------------------------------------------

Um Azure-Speicher verwenden zu können, müssen Sie das Node.js-Paket azure herunterladen und verwenden. Dieses Paket enthält eine Reihe von Bibliotheken, die mit den REST-Speicherdiensten kommunizieren.

### Verwenden von Node-Paket-Manager (NPM) zum Beziehen des Pakets

1.  Verwenden Sie eine Befehlszeilenschnittstelle, z. B. **PowerShell** (Windows,) **Terminal** (Mac) oder **Bash** (Unix), und navigieren Sie zu dem Ordner, in dem Sie die Beispielanwendung erstellt haben.

2.  Geben Sie **npm install azure** in das Befehlsfenster ein. Die Ausgabe dieses Befehls sollte wie folgt aussehen:

        azure@0.7.5 node_modules\azure
        ├── dateformat@1.0.2-1.2.3
        ├── xmlbuilder@0.4.2
        ├── node-uuid@1.2.0
        ├── mime@1.2.9
        ├── underscore@1.4.4
        ├── validator@1.1.1
        ├── tunnel@0.0.2
        ├── wns@0.5.3
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)

3.  Sie können den Befehl **ls** manuell aufrufen, um sich davon zu überzeugen, dass der Ordner **node\_modules** erstellt wurde. Dieser Ordner enthält das **azure**-Paket mit den Bibliotheken, die Sie für den Speicherzugriff benötigen.

### Importieren des Pakets

Verwenden Sie Editor oder einen anderen Texteditor, um die folgende Zeile am Anfang der Datei **server.js** der Anwendung einzufügen, in der Sie den Speicher nutzen möchten:

    var azure = require('azure');

Einrichten einer Azure-Speicherverbindung
-----------------------------------------

Das Azure-Modul entnimmt den Umgebungsvariablen AZURE\_STORAGE\_ACCOUNT und AZURE\_STORAGE\_ACCESS\_KEY die Informationen, die zum Herstellen einer Verbindung mit Ihrem Azure-Speicherkonto benötigt werden. Wenn diese Umgebungsvariablen nicht festgelegt wurden, müssen Sie die Kontoinformationen beim Aufruf von **TableService** angeben.

Ein Beispiel zum Festlegen der Umgebungsvariablen in einer Konfigurationsdatei für einen Azure-Cloud-Dienst finden Sie unter [Node.js-Clouddienst mit Speicher](/de-de/develop/nodejs/tutorials/web-app-with-storage/).

Ein Beispiel zum Festlegen der Umgebungsvariablen im Verwaltungsportal für eine Azure-Website finden Sie unter [Node.js-Webanwendung mit Speicher](/de-de/develop/nodejs/tutorials/web-site-with-storage/).

Erstellen einer Tabelle
-----------------------

Mit dem folgenden Code wird ein **TableService**-Objekt erstellt und zum Erstellen einer neuen Tabelle verwendet. Fügen Sie Folgenden am Anfang der Datei **server.js** ein.

    var tableService = azure.createTableService();

Der Aufruf von **createTableIfNotExists** gibt die angegebene Tablle zurück, wenn sie vorhanden ist, bzw. erstellt eine neue Tabelle mit dem angegebenen Namen, wenn sie nicht vorhanden ist. Im folgenden Beispiel wird eine neue Tabelle namens 'mytable' erstellt, wenn diese noch nicht vorhanden ist:

    tableService.createTableIfNotExists('mytable', function(error){
        if (!error) {
            // Tabelle ist vorhanden oder wird erstellt
        }
    });

### Filter

Auf die Vorgänge, die mit **TableService** ausgeführt werden, können optionale Filtervorgänge angewendet werden. Filtervorgänge können Protokollierung, automatische Wiederholung usw. umfassen. Filter sind Objekte, die eine Methode mit einer Signatur implementieren:

     function handle (requestOptions, next)

Nachdem die Vorverarbeitung der Anforderungsoptionen angeschlossen ist, muss die Methode "next" aufrufen und hierbei eine Rückruffunktion mit der folgenden Signatur übergeben:

     function (returnObject, finalCallback, next)

Nachdem das returnObject-Objekt (die Antwort auf die an den Server gesendete Anforderung) verarbeitet wurde, muss in dieser Rückruffunktion entweder "next" aufgerufen werden, wenn die Tabelle vorhanden ist, um weitere Filter zu verarbeiten, oder andernfalls einfach "finallCallback" aufrufen, um den Dienstaufruf zu beenden.

Zwei Filter, die eine Wiederholungslogik implementieren, sind im Azure SDK für Node.js enthalten: **ExponentialRetryPolicyFilter** und **LinearRetryPolicyFilter**. Mit folgendem Code wird ein **TableService**-Objekt, das **ExponentialRetryPolicyFilter** verwendet:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var tableService = azure.createTableService().withFilter(retryOperations);

Hinzufügen einer Entität zu einer Tabelle
-----------------------------------------

Um eine Entität hinzuzufügen, erstellen Sie zunächst ein Objekt, das die Eigenschaften der Entität und deren Datentypen definiert. Beachten Sie, dass Sie für jede Entität **PartitionKey** und **RowKey** angeben müssen. Hierbei handelt es sich um die eindeutigen Bezeichner der Entität und Werte, die viel schneller abgerufen werden können als andere Eigenschaften. Das System verwendet **PartitionKey**, um die Entitäten der Tabelle automatisch über viele Speicherknoten zu verteilen. Entitäten mit dem gleichen **PartitionKey**-Wert werden im gleichen Knoten gespeichert. Der **RowKey**-Wert ist eine eindeutige ID der Entität innerhalb der Partition, zu der sie gehört. Um eine Entität der Tabelle hinzuzufügen, übergeben Sie das Entitätsobjekt der **insertEntity**-Methode.

    var task = {
        PartitionKey : 'hometasks'
        , RowKey : '1'
        , Description : 'Take out the trash'
        , DueDate: new Date(2012, 6, 20)
    };
    tableService.insertEntity('mytable', task, function(error){
        if (!error) {
            // Entity wurde eingefügt
        }
    });

Aktualisieren einer Entität
---------------------------

Es sind mehrere Methoden zum Aktualisieren einer vorhandenen Entität vorhanden:

-   **updateEntity**: Aktualisiert eine vorhandene Entität, indem sie ersetzt wird.

-   **mergeEntity**: Aktualisiert eine vorhandene Entität durch Zusammenführen neuer Eigenschaftenwerte mit der vorhandenen Entität.

-   **insertOrReplaceEntity**: Aktualisiert eine vorhandene Entität, indem sie ersetzt wird. Wenn keine Entität vorhanden ist, wird eine neue eingefügt.

-   **insertOrMergeEntity**: Aktualisiert eine vorhandene Entität durch Zusammenführen neuer Eigenschaftenwerte mit der vorhandenen Entität. Wenn keine Entität vorhanden ist, wird eine neue eingefügt.

Das folgende Beispiel zeigt, wie eine Entität mit **updateEntity** aktualisiert wird:

    var task = {
        PartitionKey : 'hometasks'
        , RowKey : '1'
        , Description : 'Wash Dishes'
    }
    tableService.updateEntity('mytable', task, function(error){
        if (!error) {
            // Entität wurde aktualisiert
        }
    });

Bei **updateEntity** und **mergeEntity** schlägt der Aktualisierungsvorgang fehl, wenn die zu aktualisierende Entität nicht existiert. Daher sollten Sie **insertOrReplaceEntity** oder **insertOrMergeEntity** verwenden, wenn Sie eine Entität unabhängig davon speichern möchten, ob sie bereits vorhanden ist.

Arbeiten mit Gruppen von Entitäten
----------------------------------

Gelegentlich ist es sinnvoll, mehrere Vorgänge zusammen in einem Stapel zu senden, um die atomische Verarbeitung durch den Server sicherzustellen. Hierzu rufen Sie zunächst die **beginBatch**-Methode für **TableService** auf, und dann rufen Sie die Folge von Vorgängen wie üblich auf.. Der Unterschied besteht hier darin, dass die Rückruffunktionen dieser Operatoren angeben, dass der Vorgang Teil eines Stapels ist und nicht direkt an den Server gesendet wurde. Wenn Sie den Stapel übermitteln möchten, rufen Sie **commitBatch** auf. Die Rückruffunktion, die dieser Methode übergeben wird, zeigt an, ob der gesamte Stapel erfolgreich übermittelt wurde. Im folgenden Beispiel wird gezeigt, wie zwei Entitäten in einem Stapel übermittelt werden:

    var tasks=[
        {
            PartitionKey : 'hometasks'
            , RowKey : '1'
            , Description : 'Take out the trash.'
            , DueDate: new Date(2012, 6, 20)
        }
        , {
            PartitionKey : 'hometasks'
            , RowKey : '2'
            , Description : 'Wash the dishes.'
            , DueDate: new Date(2012, 6, 20)
        }
    ]
    tableService.beginBatch();
    var async=require('async');

    async.forEach(tasks
        , function taskIterator(task, callback){
            tableService.insertEntity('mytable', task, function(error){
                if (!error) {
                    // Entity wurde eingefügt
                    callback(null);
                } else {
                    callback(error);
                }
            });
        }
        , function(error){
            if (!error) {
                // Alle Einfügungen abgeschlossen
                tableService.commitBatch(function(error){
                    if (!error) {
                        // Stapel wurde erfolgreich übergeben
                    }
                });
            }
        });

**Hinweis**

Im obigen Beispiel wird durch Verwendung des Moduls 'async' sichergestellt, dass alle Entitäten vor dem Aufruf von **commitBatch** erfolgreich übermittelt wurden.

Abfragen einer Entität
----------------------

Um eine Entität in einer Tabelle abzufragen, verwenden Sie die **queryEntity**-Methode und übergeben ihr **PartitionKey** und **RowKey**.

    tableService.queryEntity('mytable'
        , 'hometasks'
        , '1'
        , function(error, entity){
            if (!error) {
                // Entität enthält die zurückgegebene Entität
            }
        });

Abfragen einer Gruppe von Entitäten
-----------------------------------

Zum Abfragen einer Tabelle erstellten Sie unter Verwendung des **TableQuery**-Objekts einen Abfrageausdruck mit Klauseln wie **select**, **from**, **where** (einschließlich Klauseln wie **wherePartitionKey**, **whereRowKey**, **whereNextPartitionKey** und **whereNextRowKey**), **and**, **or** und **top**. Dann übergeben Sie den Abfrageausdruck der **queryEntities**-Methode. Sie können die Ergebnisse in einer **for**-Schleife in der Rückruffunktion verwenden.

In diesem Beispiel werden anhand des **PartitionKey**-Werts alle Aufgaben in Seattle gefunden.

    var query = azure.TableQuery
        .select()
        .from('mytable')
        .where('PartitionKey eq 
        ', 'hometasks');
    tableService.queryEntities(query, function(error, entities){
        if (!error) {
            //Entitäten enthalten ein Array von Entitäten
        }
    });

Abfragen einer Teilmenge von Entitätseigenschaften
--------------------------------------------------

Mit einer Tabellenabfrage können nur einige wenige Eigenschaften von einer Entität abgefragt werden. Mit dieser Technik, der sogenannten *Projektion*, wird die Bandbreite reduziert und die Abfrageleistung gesteigert, vor allem bei großen Entitäten. Verwenden Sie die **select**-Klausel, und übergeben Sie die Namen der Eigenschaften, die an den Client übermittelt werden sollen.

Mit der Abfrage im folgenden Code werden nur die **Descriptions**-Werte der Entitäten in der Tabelle zurückgegeben. Beachten Sie, dass **DueDate** in der Programmausgabe als **undefined** erscheint, weil es vom Server nicht gesendet wurde.

**Hinweis**

Beachten Sie, dass der folgende Codeausschnitt nur mit dem Cloud-Speicherdienst funktioniert, da das Schlüsselwort **select** vom Speicheremulator nicht unterstützt wird.

    var query = azure.TableQuery
        .select('Description')
        .from('mytable')
        .where('PartitionKey eq 
        ', 'hometasks');
    tableService.queryEntities(query, function(error, entities){
        if (!error) {
            //Entitäten enthalten ein Array von Entitäten
        }
    });

Löschen einer Entität
---------------------

Sie können eine Entität unter Verwendung ihres Partitions- und Zeilenschlüssels löschen. In diesem Beispiel enthält das Objekt **task1** die **RowKey**- und **PartitionKey**-Werte der zu löschenden Entität. Dann wird das Objekt der **deleteEntity**-Methode übergeben.

    tableService.deleteEntity('mytable'
        , {
            PartitionKey : 'hometasks'
            , RowKey : '1'
        }
        , function(error){
            if (!error) {
                // Entität wurde gelöscht
            }
        });

Löschen einer Tabelle
---------------------

Mit dem folgenden Code wird eine Tabelle aus einem Speicherkonto gelöscht.

    tableService.deleteTable('mytable', function(error){
        if (!error) {
            // Tabelle wurde gelöscht
        }
    });

Nächste Schritte
----------------

Nachdem Sie sich nun mit den Grundlagen von Tabellenspeicher vertraut gemacht haben, folgen Sie diesen Links, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Speichern und Zugreifen auf Daten in Azure](http://msdn.microsoft.com/de-de/library/windowsazure/gg433040.aspx).
-   [Besuchen Sie den Blog des Azure-Speicherteams](http://blogs.msdn.com/b/windowsazurestorage/).
-   Besuchen Sie das [Azure SDK für Node](https://github.com/WindowsAzure/azure-sdk-for-node)-Repository auf GitHub.

