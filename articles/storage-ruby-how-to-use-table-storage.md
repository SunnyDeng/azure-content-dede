<properties 
	pageTitle="Verwenden des Tabellenspeichers mit Ruby | Microsoft Azure" 
	description="Erfahren Sie, wie Sie den Tabellenspeicherdienst in Azure verwenden. Die Codebeispiele wurden mithilfe der Ruby-API geschrieben."
	services="storage" 
	documentationCenter="ruby" 
	authors="tfitzmac,tamram" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="03/11/2015" 
	ms.author="tomfitz"/>


# Verwenden des Tabellenspeichers mit Ruby

[AZURE.INCLUDE [storage-selector-table-include](../includes/storage-selector-table-include.md)]

## Übersicht

In diesem Leitfaden wird die Durchführung häufiger Szenarien mit dem
Azure-Tabellenspeicherdienst demonstriert. Die Beispiele wurden mit
der Ruby-API geschrieben. Die behandelten Szenarien umfassen das **Erstellen und Löschen einer Tabelle sowie das Einfügen und Abfragen von Tabellenentitäten**.

[AZURE.INCLUDE [storage-table-concepts-include](../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]

## Erstellen einer Ruby-Anwendung

Erstellen Sie eine Ruby-Anwendung. Anweisungen finden Sie im Thema zum [Erstellen einer Ruby-Anwendung in Azure](/develop/ruby/tutorials/web-app-with-linux-vm/).

## Konfigurieren der Anwendung für den Speicherzugriff

Um den Azure-Speicher zu verwenden, müssen Sie das Ruby-Azure-Paket, das eine Reihe von Bibliotheken enthält, die mit den Speicher-REST-Diensten kommunizieren, herunterladen und verwenden.

### Verwenden von RubyGems zum Abrufen des Pakets

1. Verwenden Sie eine Befehlszeilenschnittstelle wie **PowerShell** (Windows), **Terminal** (Mac) oder **Bash** (Unix).

2. Geben Sie im Befehlsfenster **gem install azure** ein, um das Gem und Abhängigkeiten zu installieren.

### Importieren des Pakets

Fügen Sie mit Ihrem bevorzugten Texteditor Folgendes oben in die Ruby-Datei an der Stelle ein, an der Sie den Speicher verwenden möchten:

	require "azure"

## Einrichten einer Azure-Speicherverbindung

Das Azure-Modul entnimmt den Umgebungsvariablen **AZURE_STORAGE_ACCOUNT** und **AZURE_STORAGE_ACCESS_KEY** die Informationen, die zum Herstellen einer Verbindung mit Ihrem Azure-Speicherkonto benötigt werden. Wenn diese Umgebungsvariablen nicht festgelegt werden, müssen Sie die Kontoinformationen vor dem Verwenden von **Azure::TableService** mit dem folgenden Code angeben:

	Azure.config.storage_account_name = "<your azure storage account>"
	Azure.config.storage_access_key = "<your azure storage access key>"

So rufen Sie diese Werte ab:

1. Melden Sie sich am [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an.

2. Navigieren Sie zum Speicherkonto, das Sie verwenden möchten.

3. Klicken Sie unten im Navigationsbereich auf **SCHLÜSSEL VERWALTEN**.

4. Im eingeblendeten Dialog werden der Name des Speicherkontos, der primäre Zugriffsschlüssel und der sekundäre Zugriffsschlüssel angezeigt. Als Zugriffsschlüssel können Sie den primären oder sekundären auswählen.

## Erstellen einer Tabelle

Mit dem **Azure::TableService**-Objekt können Sie mit Tabellen und Entitäten arbeiten. Verwenden Sie die **create_table()**-Methode, um eine Tabelle zu erstellen. Im folgenden Beispiel wird eine Tabelle erstellt oder ggf. ein Fehler ausgegeben.

	azure_table_service = Azure::TableService.new
	begin
	 azure_table_service.create_table("testtable")
	rescue
	 puts $!
	end

## Hinzufügen einer Entität zu einer Tabelle

Um eine Entität hinzuzufügen, erstellen Sie zunächst ein Hashobjekt, das die Entitätseigenschaften definiert. Beachten Sie, dass Sie für jede Entität **PartitionKey** und **RowKey** angeben müssen. Hierbei handelt es sich um die eindeutigen Bezeichner der Entität und Werte, die viel schneller abgerufen werden können als andere Eigenschaften. Azure Storage Service verwendet **PartitionKey**, um die Entitäten der Tabelle automatisch über viele Speicherknoten zu verteilen. Entitäten mit dem gleichen **PartitionKey** werden auf dem gleichen Knoten gespeichert. Der **RowKey**-Wert ist eine eindeutige ID der Entität innerhalb der Partition, zu der sie gehört. 

	entity = { "content" => "test entity", 
	  :PartitionKey => "test-partition-key", :RowKey => "1" }
	azure_table_service.insert_entity("testtable", entity)

## Vorgehensweise: Aktualisieren einer Entität

Es sind mehrere Methoden zum Aktualisieren einer vorhandenen Entität vorhanden:

* **update_entity():** Aktualisiert eine vorhandene Entität, indem sie ersetzt wird.
* **merge_entity():**: Aktualisiert eine vorhandene Entität durch Zusammenführen neuer Eigenschaftenwerte mit der vorhandenen Entität.
* **insert_or_merge_entity():** Aktualisiert eine vorhandene Entität, indem sie ersetzt wird. Wenn keine Entität vorhanden ist, wird eine neue eingefügt:
* **insert_or_replace_entity():** Aktualisiert eine vorhandene Entität durch Zusammenführen neuer Eigenschaftenwerte mit der vorhandenen Entität. Wenn keine Entität vorhanden ist, wird eine neue eingefügt.

Das folgende Beispiel zeigt, wie eine Entität mit **update_entity()** aktualisiert wird:

	entity = { "content" => "test entity with updated content", 
	  :PartitionKey => "test-partition-key", :RowKey => "1" }
	azure_table_service.update_entity("testtable", entity)

Bei **update_entity()** und **merge_entity()** missling der Aktualisierungsvorgang, wenn die zu aktualisierende Entität nicht vorhanden ist. Wenn Sie daher eine Entität unabhängig davon speichern möchten, ob sie bereits vorhanden ist, sollten Sie stattdessen **insert_or_replace_entity()** oder **insert_or_merge_entity()** verwenden.

## Vorgehensweise: Arbeiten mit Gruppen von Entitäten

Gelegentlich ist es sinnvoll, mehrere Vorgänge zusammen in einem Stapel zu senden, um die atomische Verarbeitung durch den Server sicherzustellen. Dazu erstellen Sie zunächst ein **Batch**-Objekt und wenden dann die **execute_batch()**-Methode auf **TableService** an. Das folgende Beispiel demonstriert das Senden von zwei Entitäten mit RowKey 2 und 3 in einem Batch. Beachten Sie, dass dies nur für Entitäten mit dem gleichen PartitionKey funktioniert.

	azure_table_service = Azure::TableService.new
	batch = Azure::Storage::Table::Batch.new("testtable", 
	  "test-partition-key") do
	  insert "2", { "content" => "new content 2" }
	  insert "3", { "content" => "new content 3" }
	end
	results = azure_table_service.execute_batch(batch)

## Vorgehensweise: Abfragen einer Entität

Um eine Entität in einer Tabelle abzufragen, verwenden Sie die **get_entity()**-Methode und übergeben den Tabellennamen, **PartitionKey** und **RowKey** an sie.

	result = azure_table_service.get_entity("testtable", "test-partition-key", 
	  "1")

## Vorgehensweise: Abfragen einer Gruppe von Entitäten

Um eine Gruppe von Entitäten in einer Tabelle abzufragen, erstellen Sie ein Hashobjekt und verwenden die **query_entities()**-Methode. Das folgende Beispiel demonstriert das Abrufen aller Entitäten mit dem gleichen **PartitionKey**:

	query = { :filter => "PartitionKey eq 'test-partition-key'" }
	result, token = azure_table_service.query_entities("testtable", query)

**Hinweis:** Wenn die Ergebnisgruppe zu groß für die Rückgabe einer einzelnen Abfrage ist, wird ein Fortsetzungstoken zurückgegeben, das Sie verwenden können, um nachfolgende Seiten abzurufen.

## Vorgehensweise: Abfragen einer Teilmenge von Entitäteneigenschaften

Mit einer Abfrage einer Tabelle können nur einige wenige Eigenschaften einer Entität aufgerufen werden. Bei dieser Methode, der so genannten Projektion, wird die Bandbreite reduziert und die Abfrageleistung gesteigert, vor allem bei großen Entitäten. Verwenden Sie die select-Klausel, und übergeben Sie die Namen der Eigenschaften, die an den Client übermittelt werden sollen.

	query = { :filter => "PartitionKey eq 'test-partition-key'", 
	  :select => ["content"] }
	result, token = azure_table_service.query_entities("testtable", query)

## Vorgehensweise: Löschen einer Entität

Verwenden Sie die **delete_entity()**-Methode, um eine Entität zu löschen. Sie müssen den Namen der Tabelle mit der Entität, dem PartitionKey und dem RowKey der Entität übergeben.

		azure_table_service.delete_entity("testtable", "test-partition-key", "1")

## Vorgehensweise: Löschen einer Tabelle

Um eine Tabelle zu löschen, verwenden Sie die **delete_table()**-Methode und übergeben an sie den Namen der zu löschenden Tabelle.

		azure_table_service.delete_table("testtable")

## Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen des Tabellenspeichers vertraut gemacht haben, folgen Sie diesen Links, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.

- Weitere Informationen finden Sie in der MSDN-Referenz: [Azure Storage](http://msdn.microsoft.com/library/azure/gg433040.aspx)
- Besuchen Sie den [Azure Storage-Teamblog](http://blogs.msdn.com/b/windowsazurestorage/).
- Besuchen Sie das [Azure SDK for Ruby](http://github.com/WindowsAzure/azure-sdk-for-ruby)-Repository auf GitHub.

<!--HONumber=49-->