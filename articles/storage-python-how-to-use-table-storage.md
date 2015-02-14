<properties 
	pageTitle="Verwenden des Tabellenspeichers (Python) | Microsoft Azure" 
	description="Erfahren Sie, wie Sie den Tabellendienst aus Python zum Erstellen und Löschen von Tabellen sowie zum Einfügen und Abfragen in der Tabelle verwenden." 
	services="storage" 
	documentationCenter="python" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="09/19/2014" 
	ms.author="robmcm"/>





# Verwenden des Tabellenspeicherdiensts aus Python
Dieser Leitfaden demonstriert die Durchführung häufiger Szenarien mit dem Azure-Tabellenspeicherdienst. Die Beispiele wurden unter Verwendung der Python-API geschrieben. Die behandelten Szenarien umfassen das **Erstellen und Löschen einer Tabelle sowie das Einfügen und Abfragen von Tabellenentitäten**. Weitere Informationen zu Tabellen finden Sie im Abschnitt [Nächste Schritte][].

## Inhaltsverzeichnis

[Was ist der Tabellenspeicherdienst?][]   
 [Konzepte][]   
 [Erstellen eines Azure-Speicherkontos][]   
 [Vorgehensweise: Erstellen einer Tabelle][]   
 [Vorgehensweise: Hinzufügen einer Entität zu einer Tabelle][]   
 [Vorgehensweise: Aktualisieren einer Entität][]   
 [Vorgehensweise: Ändern einer Gruppe von Entitäten][]   
 [Vorgehensweise: Abfragen einer Entität][]   
 [Vorgehensweise: Abfragen einer Gruppe von Entitäten][]   
 [Vorgehensweise: Abfragen einer Teilmenge von Entitäteneigenschaften][]   
 [Vorgehensweise: Löschen einer Entität][]   
 [Vorgehensweise: Löschen einer Tabelle][]   
 [Nächste Schritte][]

[AZURE.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

## <a name="create-account"> </a>Erstellen eines Azure-Speicherkontos
[AZURE.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

**Hinweis:** Informationen zur Installation von Python oder den Clientbibliotheken finden Sie im [Python-Installationshandbuch](../python-how-to-install/).


## <a name="create-table"> </a>Erstellen einer Tabelle

Das **TableService**-Objekt ermöglicht Ihnen das Arbeiten mit Tabellenspeicherdiensten. Der folgende Code erstellt ein **TableService**-Objekt. Fügen Sie am Anfang jeder Python-Datei, in der Sie programmgesteuert auf Azure-Speicher zugreifen möchten, den folgenden Code hinzu:

	from azure.storage import TableService, Entity

Der folgende Code erstellt ein **TableService**-Objekt unter Verwendung des Speicherkontonamens und Kontoschlüssels.  Ersetzen Sie 'myaccount' und 'mykey' durch das tatsächliche Konto und den tatsächlichen Schlüssel.

	table_service = TableService(account_name='myaccount', account_key='mykey')

	table_service.create_table('tasktable')

## <a name="add-entity"> </a>Hinzufügen einer Entität zu einer Tabelle

Um eine Entität hinzuzufügen, erstellen Sie zunächst ein Wörterbuch, das die Eigenschaftsnamen der Entität und deren Werte definiert. Beachten Sie, dass Sie für jede Entität **PartitionKey** und **RowKey** angeben müssen. Hierbei handelt es sich um die eindeutigen Bezeichner der Entität und Werte, die viel schneller abgerufen werden können als andere Eigenschaften. Das System verwendet **PartitionKey**, um die Entitäten der Tabelle automatisch über viele Speicherknoten zu verteilen.
Entitäten mit dem gleichen **PartitionKey** werden auf dem gleichen Knoten gespeichert. Der **RowKey**-Wert ist eine eindeutige ID der Entität innerhalb der Partition, zu der sie gehört.

Um eine Entität zu Ihrer Tabelle hinzuzufügen, übergeben Sie das Wörterbuchobjekt an die **insert\_entity**-Methode.

	task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the trash', 'priority' : 200}
	table_service.insert_entity('tasktable', task)

Sie können auch eine Instanz der **Entity**-Klasse an die **insert\_entity**-Methode übergeben.

	task = Entity()
	task.PartitionKey = 'tasksSeattle'
	task.RowKey = '2'
	task.description = 'Wash the car'
	task.priority = 100
	table_service.insert_entity('tasktable', task)

## <a name="update-entity"> </a>Aktualisieren einer Entität

Dieser Code zeigt, wie Sie die alte Version einer existierenden Entität durch eine neue Version ersetzen können.

	task = {'description' : 'Take out the garbage', 'priority' : 250}
	table_service.update_entity('tasktable', 'tasksSeattle', '1', task)

Der Aktualisierungsvorgang schlägt fehl, wenn die zu aktualisierende Entität nicht existiert. Daher sollten Sie **insert\_or\_replace_entity** verwenden, wenn Sie eine Entität unabhängig davon speichern möchten, ob diese bereits vorhanden ist. 
Der erste Aufruf im folgenden Beispiel ersetzt die existierende Entität. Der zweite Aufruf fügt eine neue Entität ein, da keine Entität mit dem angegebenen **PartitionKey** und **RowKey** in der Tabelle existiert.

	task = {'description' : 'Take out the garbage again', 'priority' : 250}
	table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task)

	task = {'description' : 'Buy detergent', 'priority' : 300}
	table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '3', task)

## <a name="change-entities"> </a>Ändern einer Gruppe von Entitäten

Gelegentlich ist es sinnvoll, mehrere Vorgänge zusammen in einem Stapel zu senden, um die atomische Verarbeitung durch den Server sicherzustellen. Hierzu rufen Sie zunächst die **begin\_batch**-Methode für **TableService** auf, und dann rufen Sie die Folge von Vorgängen wie üblich auf. Wenn Sie den Batch übermitteln möchten, rufen Sie **commit\_batch** auf. Beachten Sie, dass sich alle Entitäten in derselben Partition befinden müssen, um diese als Batch ändern zu können. Das folgende Beispiel fügt zwei Entitäten als Batch ein.

	task10 = {'PartitionKey': 'tasksSeattle', 'RowKey': '10', 'description' : 'Go grocery shopping', 'priority' : 400}
	task11 = {'PartitionKey': 'tasksSeattle', 'RowKey': '11', 'description' : 'Clean the bathroom', 'priority' : 100}
	table_service.begin_batch()
	table_service.insert_entity('tasktable', task10)
	table_service.insert_entity('tasktable', task11)
	table_service.commit_batch()

## <a name="query-for-entity"> </a>Abfragen einer Entität

Um eine Entität in einer Tabelle abzufragen, verwenden Sie die **get\_entity**-Methode und übergeben ihr **PartitionKey** und **RowKey**.

	task = table_service.get_entity('tasktable', 'tasksSeattle', '1')
	print(task.description)
	print(task.priority)

## <a name="query-set-entities"> </a>Abfragen einer Gruppe von Entitäten

Dieses Beispiel fragt alle Aufgaben in Seattle anhand des **PartitionKey**-Werts ab.

	tasks = table_service.query_entities('tasktable', "PartitionKey eq 'tasksSeattle'")
	for task in tasks:
		print(task.description)
		print(task.priority)

## <a name="query-entity-properties"> </a>Abfragen einer Teilmenge von Entitätseigenschaften

Mit einer Abfrage einer Tabelle können nur einige wenige Eigenschaften einer Entität aufgerufen werden.
Bei dieser Methode, der sogenannten *Projektion*, wird der Bandbreitenbedarf reduziert und die Abfrageleistung gesteigert, vor allem bei großen Entitäten. Verwenden Sie den **select**-Parameter, und übergeben Sie die Namen der Eigenschaften, die an den Client übermittelt werden sollen.

Mit der Abfrage im folgenden Code werden nur die **Beschreibungen** von Entitäten in der Tabelle zurückgegeben.

Beachten Sie, dass der folgende Codeausschnitt nur mit dem Cloud-Speicherdienst funktioniert, da diese Funktion vom Speicheremulator
nicht unterstützt wird.*

	tasks = table_service.query_entities('tasktable', "PartitionKey eq 'tasksSeattle'", 'description')
	for task in tasks:
		print(task.description)

## <a name="delete-entity"> </a>Löschen einer Entität

Sie können eine Entität unter Verwendung ihres Partitions- und Zeilenschlüssels löschen.

	table_service.delete_entity('tasktable', 'tasksSeattle', '1')

## <a name="delete-table"> </a>Löschen einer Tabelle

Mit dem folgenden Code wird eine Tabelle aus einem Speicherkonto gelöscht.

	table_service.delete_table('tasktable')

## <a name="next-steps"> </a>Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen der Tabellenspeicherung vertraut gemacht haben, folgen Sie diesen Links, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Speichern und Zugreifen auf Daten in Azure][]
-   [Besuchen Sie den Azure Storage-Teamblog][].

  [Nächste Schritte]: #next-steps
  [Was ist der Tabellenspeicherdienst?]: #what-is
  [Konzepte]: #concepts
  [Erstellen eines Azure-Speicherkontos]: #create-account
  [Vorgehensweise: Erstellen einer Tabelle]: #create-table
  [Vorgehensweise: Hinzufügen einer Entität zu einer Tabelle]: #add-entity
  [Vorgehensweise: Aktualisieren einer Entität]: #update-entity
  [Vorgehensweise: Ändern einer Gruppe von Entitäten]: #change-entities
  [Vorgehensweise: Abfragen einer Entität]: #query-for-entity
  [Vorgehensweise: Abfragen einer Gruppe von Entitäten]: #query-set-entities
  [Vorgehensweise: Abfragen einer Teilmenge von Entitäteneigenschaften]: #query-entity-properties
  [Vorgehensweise: Löschen einer Entität]: #delete-entity
  [Vorgehensweise: Löschen einer Tabelle]: #delete-table
  [Speichern und Zugreifen auf Daten in Azure]: http://msdn.microsoft.com/de-de/library/windowsazure/gg433040.aspx
  [Besuchen Sie den Azure Storage-Teamblog].: http://blogs.msdn.com/b/windowsazurestorage/

<!--HONumber=42-->
