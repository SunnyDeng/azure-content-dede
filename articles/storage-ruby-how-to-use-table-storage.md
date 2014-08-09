<properties linkid="dev-ruby-how-to-table-services" urlDisplayName="Table Service" pageTitle="How to use table storage (Ruby) | Microsoft Azure" metaKeywords="Azure table storage service, Azure table service Ruby, table storage Ruby" description="Learn how to use the table storage service in Azure. Code samples are written using the Ruby API." metaCanonical="" services="storage" documentationCenter="Ruby" title="How to Use the Table Service from Ruby" authors="guayan" solutions="" manager="" editor="" />

Verwenden des Tabellenspeicherdiensts von Ruby
==============================================

In diesem Leitfaden wird die Durchführung häufiger Szenarien mit dem Azure-Tabellenspeicherdienst demonstriert. Die Beispiele wurden mit der Ruby-API erstellt. Die behandelten Szenarien umfassen das **Erstellen und Löschen einer Tabelle sowie das Einfügen und Abfragen von Tabellenentitäten**. Weitere Informationen zu Tabellen finden Sie im Abschnitt [Nächste Schritte](#next-steps).

Inhaltsverzeichnis
------------------

-   [Was ist der Tabellenspeicherdienst?](#what-is)
-   [Konzepte](#concepts)
-   [Erstellen eines Azure-Speicherkontos](#create-a-windows-azure-storage-account)
-   [Erstellen einer Ruby-Anwendung](#create-a-ruby-application)
-   [Konfigurieren der Anwendung für den Speicherzugriff](#configure-your-application-to-access-storage)
-   [Einrichten einer Azure-Speicherverbindung](#setup-a-windows-azure-storage-connection)
-   [Gewusst wie: Erstellen einer Tabelle](#how-to-create-a-table)
-   [Gewusst wie: Hinzufügen einer Entität zu einer Tabelle](#how-to-add-an-entity-to-a-table)
-   [Gewusst wie: Aktualisieren einer Entität](#how-to-update-an-entity)
-   [Gewusst wie: Arbeiten mit Gruppen von Entitäten](#how-to-work-with-groups-of-entities)
-   [Gewusst wie: Abfragen einer Entität](#how-to-query-for-an-entity)
-   [Gewusst wie: Abfragen einer Gruppe von Entitäten](#how-to-query-a-set-of-entities)
-   [Gewusst wie: Abfragen einer Teilmenge von Entitäteneigenschaften](#how-to-query-a-subset-of-entity-properties)
-   [Gewusst wie: Löschen einer Entität](#how-to-delete-an-entity)
-   [Gewusst wie: Löschen einer Tabelle](#how-to-delete-a-table)
-   [Nächste Schritte](#next-steps)

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

Erstellen eines Azure-Speicherkontos
------------------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Erstellen einer Ruby-Anwendung
------------------------------

Erstellen Sie eine Ruby-Anwendung. Anweisungen finden Sie im Thema zum [Erstellen einer Ruby-Anwendung in Azure](/de-de/develop/ruby/tutorials/web-app-with-linux-vm/).

Konfigurieren der Anwendung für den Speicherzugriff
---------------------------------------------------

Um den Azure-Speicher zu verwenden, müssen Sie das Ruby-Azure-Paket, das eine Reihe von Bibliotheken enthält, die mit den Speicher-REST-Diensten kommunizieren, herunterladen und verwenden.

### Verwenden von RubyGems zum Abrufen des Pakets

1.  Verwenden Sie eine Befehlszeilenschnittstelle wie **PowerShell** (Windows), **Terminal** (Mac) oder **Bash** (Unix).

2.  Geben Sie im Befehlsfenster **gem install azure** ein, um das Gem und Abhängigkeiten zu installieren.

### Importieren des Pakets

Fügen Sie mit Ihrem bevorzugten Texteditor Folgendes oben in die Ruby-Datei an der Stelle ein, an der Sie den Speicher verwenden möchten:

    require "azure"

Einrichten einer Azure-Speicherverbindung
-----------------------------------------

Das Azure-Modul liest die Umgebungsvariablen **AZURE\_STORAGE\_ACCOUNT** und **AZURE\_STORAGE\_ACCESS\_KEY** nach Informationen aus, die erforderlich sind, um eine Verbindung zum Azure-Speicherkonto herzustellen. Wenn diese Umgebungsvariablen nicht festgelegt werden, müssen Sie die Kontoinformationen vor dem Verwenden von **Azure::TableService** mit dem folgenden Code angeben:

    Azure.config.storage_account_name = "<your azure storage account>"
    Azure.config.storage_access_key = "<your azure storage access key>"

So rufen Sie diese Werte ab:

1.  Melden Sie sich auf dem [Windows Azure-Verwaltungsportal](https://manage.windowsazure.com/) an.

2.  Navigieren Sie zum Speicherkonto, das Sie verwenden möchten.

3.  Klicken Sie unter im Navigationsbereich auf **MANAGE KEYS**.

4.  Im eingeblendeten Dialog wird der Name des Speicherkontos, der primäre Zugriffsschlüssel und der sekundäre Zugriffsschlüssel angezeigt. Als Zugriffsschlüssel können Sie den primären oder sekundären auswählen.

Erstellen einer Tabelle
-----------------------

Mit dem **Azure::TableService**-Objekt können Sie mit Tabellen und Entitäten arbeiten. Verwenden Sie die **create\_table()**-Methode, um eine Tabelle zu erstellen. Im folgenden Beispiel wird eine Tabelle erstellt oder ggf. ein Fehler ausgegeben.

    azure_table_service = Azure::TableService.new
    begin
      azure_table_service.create_table("testtable")
    rescue
      puts $!
    end

Hinzufügen einer Entität zu einer Tabelle
-----------------------------------------

Um eine Entität hinzuzufügen, erstellen Sie zunächst ein Hashobjekt, das die Entitätseigenschaften definiert. Beachten Sie, dass Sie für jede Entität **PartitionKey** und **RowKey** angeben müssen. Hierbei handelt es sich um die eindeutigen Bezeichner der Entität und Werte, die viel schneller abgerufen werden können als andere Eigenschaften. Azure Storage Service verwendet **PartitionKey**, um die Entitäten der Tabelle automatisch über viele Speicherknoten zu verteilen. Entitäten mit dem gleichen **PartitionKey** werden auf dem gleichen Knoten gespeichert. Der **RowKey**-Wert ist eine eindeutige ID der Entität innerhalb der Partition, zu der sie gehört.

    entity = { "content" => "test entity", 
      :PartitionKey => "test-partition-key", :RowKey => "1" }
    azure_table_service.insert_entity("testtable", entity)

Gewusst wie: Aktualisieren einer Entität
----------------------------------------

Es sind mehrere Methoden zum Aktualisieren einer vorhandenen Entität vorhanden:

-   **update\_entity():** Aktualisiert einer vorhandene Entität, indem sie ersetzt wird.
-   **merge\_entity():** Aktualisiert eine vorhandene Entität durch Zusammenführen neuer Eigenschaftenwerte mit der vorhandenen Entität.
-   **insert\_or\_merge\_entity():** Aktualisiert einer vorhandene Entität, indem sie ersetzt wird. Wenn keine Entität vorhanden ist, wird eine neue eingefügt:
-   **insert\_or\_replace\_entity():** Aktualisiert eine vorhandene Entität durch Zusammenführen neuer Eigenschaftenwerte mit der vorhandenen Entität. Wenn keine Entität vorhanden ist, wird eine neue eingefügt.

Das folgende Beispiel zeigt, wie eine Entität mit **update\_entity()** aktualisiert wird:

    entity = { "content" => "test entity with updated content", 
      :PartitionKey => "test-partition-key", :RowKey => "1" }
    azure_table_service.update_entity("testtable", entity)

Mit **update\_entity()** und **merge\_entity()** schlägt der Aktualisierungsvorgang fehl, wenn die zu aktualisierende Entität nicht vorhanden ist. Wenn Sie daher eine Entität unabhängig davon speichern möchten, ob sie bereits vorhanden ist, sollten Sie stattdessen **insert\_or\_replace\_entity()** oder **insert\_or\_merge\_entity()** verwenden.

Gewusst wie: Arbeiten mit Gruppen von Entitäten
-----------------------------------------------

Gelegentlich ist es sinnvoll, mehrere Vorgänge zusammen in einem Stapel zu senden, um die atomische Verarbeitung durch den Server sicherzustellen. Dazu erstellen Sie zunächst ein **Batch**-Objekt und verwenden dann die **execute\_batch()**-Methode für **TableService**. Das folgende Beispiel demonstriert das Senden von zwei Entitäten mit RowKey 2 und 3 in einem Batch. Beachten Sie, dass dies nur für Entitäten mit dem gleichen PartitionKey funktioniert.

    azure_table_service = Azure::TableService.new
    batch = Azure::Storage::Table::Batch.new("testtable", 
      "test-partition-key") do
      insert "2", { "content" => "new content 2" }
      insert "3", { "content" => "new content 3" }
    end
    results = azure_table_service.execute_batch(batch)

Gewusst wie: Abfragen einer Entität
-----------------------------------

Um eine Entität in einer Tabelle abzufragen, verwenden Sie die **get\_entity()**-Methode und übergeben ihr **PartitionKey** und **RowKey**.

    result = azure_table_service.get_entity("testtable", "test-partition-key", 
      "1")

Gewusst wie: Abfragen einer Gruppe von Entitäten
------------------------------------------------

Um eine Gruppe von Entitäten in einer Tabelle abzufragen, erstellen Sie ein Hashobjekt und verwenden die **query\_entities()**-Methode. Das folgende Beispiel demonstriert das Abrufen aller Entitäten mit dem gleichen **PartitionKey**:

    query = { :filter => "PartitionKey eq 'test-partition-key'" }
    result, token = azure_table_service.query_entities("testtable", query)

**Beachten Sie**, dass, wenn die Ergebnisgruppe zu groß für die Rückgabe einer einzelnen Abfrage ist, ein Fortsetzungstoken zurückgegeben wird, das Sie verwenden können, um nachfolgende Seiten abzurufen.

Gewusst wie: Abfragen einer Teilmenge von Entitäteneigenschaften
----------------------------------------------------------------

Mit einer Abfrage einer Tabelle können nur einige wenige Eigenschaften einer Entität aufgerufen werden. Bei dieser Methode, der so genannten Projektion, wird die Bandbreite reduziert und die Abfrageleistung gesteigert, vor allem bei großen Entitäten. Verwenden Sie die select-Klausel, und übergeben Sie die Namen der Eigenschaften, die an den Client übermittelt werden sollen.

    query = { :filter => "PartitionKey eq 'test-partition-key'", 
      :select => ["content"] }
    result, token = azure_table_service.query_entities("testtable", query)

Gewusst wie: Löschen einer Entität
----------------------------------

Verwenden Sie die **delete\_entity()**-Methode, um eine Entität zu löschen. Sie müssen den Namen der Tabelle mit der Entität, dem PartitionKey und dem RowKey der Entität übergeben.

     azure_table_service.delete_entity("testtable", "test-partition-key", "1")

Gewusst wie: Löschen einer Tabelle
----------------------------------

Um eine Tabelle zu löschen, verwenden Sie die **delete\_table()**-Methode, und übergeben Sie den Namen der zu löschenden Tabelle.

     azure_table_service.delete_table("testtable")

Nächste Schritte
----------------

Nachdem Sie sich nun mit den Grundlagen der Tabellenspeicherung vertraut gemacht haben, folgen Sie diesen Links, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Speichern und Zugreifen auf Daten in Azure](http://msdn.microsoft.com/de-de/library/windowsazure/gg433040.aspx)
-   Besuchen Sie den [Blog des Azure-Speicherteams](http://blogs.msdn.com/b/windowsazurestorage/)
-   Besuchen Sie das [Azure SDK for Ruby](http://github.com/WindowsAzure/azure-sdk-for-ruby)-Repository auf GitHub

