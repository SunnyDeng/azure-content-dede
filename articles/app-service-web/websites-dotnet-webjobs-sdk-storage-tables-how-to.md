<properties 
	pageTitle="Verwenden von Azure-Tabellenspeicher mit dem Webaufträge-SDK" 
	description="Erfahren Sie, wie Sie Azure-Tabellenspeicher mit dem Webaufträge-SDK nutzen. Erstellen von Tabellen, Entitäten zu Tabellen hinzufügen und vorhandene Tabellen lesen." 
	services="app-service\web, storage" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/03/2015" 
	ms.author="tdykstra"/>

# Verwenden von Azure-Tabellenspeicher mit dem Webaufträge-SDK

## Übersicht

Dieses Handbuch enthält die c\#-Codebeispiele, die zeigen, wie Sie lesen und Schreiben von Azure-Speichertabellen mit [WebJobs SDK](websites-dotnet-webjobs-sdk.md) Version 1.x.

Das Handbuch wird davon ausgegangen, Sie wissen [wie ein WebJob-Projekt in Visual Studio mit Verbindung erstellt Zeichenfolgen, die auf Ihr Speicherkonto](websites-dotnet-webjobs-sdk-get-started.md).
		
Einige der Code Snippets Anzeigen der `Table` in Funktionen verwendeten [manuell aufgerufen](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#manual), d. h. nicht mithilfe eines der Attribute des Triggers.

## <a id="ingress"></a> Gewusst wie: Hinzufügen von Entitäten zu einer Tabelle

Um Entitäten zu einer Tabelle hinzuzufügen, verwenden die `Table` Attribut mit einer `ICollector<T>` oder `IAsyncCollector<T>` Parameter, in denen `T` Gibt das Schema für die Entitäten, die Sie hinzufügen möchten. Der Attributkonstruktor hat einen Zeichenfolgenparameter, der den Namen der Tabelle angibt.

Das folgende Codebeispiel fügt `Person` Entitäten in eine Tabelle mit dem Namen *eingehend*.

		[NoAutomaticTrigger]
		public static void IngressDemo(
		    [Table("Ingress")] ICollector<Person> tableBinding)
		{
		    for (int i = 0; i < 100000; i++)
		    {
		        tableBinding.Add(
		            new Person() { 
		                PartitionKey = "Test", 
		                RowKey = i.ToString(), 
		                Name = "Name" }
		            );
		    }
		}

In der Regel den Typ Verwendung mit `ICollector` leitet sich von `TableEntity` oder implementiert `ITableEntity`, muss aber nicht. Eines der folgenden `Person` Arbeit durch den Code im vorangehenden Klassen `Ingress` Methode.

		public class Person : TableEntity
		{
		    public string Name { get; set; }
		}

		public class Person
		{
		    public string PartitionKey { get; set; }
		    public string RowKey { get; set; }
		    public string Name { get; set; }
		}

Wenn Sie direkt mit den Azure-Speicher-API arbeiten möchten, können Sie Hinzufügen einer `CloudStorageAccount` Parameter der Methodensignatur.

## <a id="monitor"></a> Echtzeit-Überwachung

Da Dateneingangsfunktionen oft große Datenmengen verarbeiten, bietet das Dashboard des Webaufträge-SDK Überwachungsdaten in Echtzeit. Die **Aufruf Protokoll** Abschnitt erfahren Sie, wenn die Funktion weiterhin ausgeführt wird.

![Eingangsfunktion wird ausgeführt](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingressrunning.png)

Die **Aufruf Details** Seite meldet die Funktion ausgeführt (Anzahl der Entitäten geschrieben), während es ausgeführt wird, und bietet Ihnen die Möglichkeit, ihn abzubrechen.

![Eingangsfunktion wird ausgeführt](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingressprogress.png)

Wenn die Funktion abgeschlossen ist, die **Aufruf Details** Seite gibt die Anzahl der geschriebenen Zeilen.

![Eingangsfunktion ist abgeschlossen](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingresssuccess.png)

## <a id="multiple"></a> Gewusst wie: Lesen von mehreren Entitäten aus einer Tabelle

Verwenden Sie zum Lesen einer Tabelle die `Table` -Attribut mit einem `IQueryable<T>` Parameter geben, in denen `T` abgeleitet `TableEntity` oder implementiert `ITableEntity`.

Das folgende Codebeispiel liest und meldet alle Zeilen aus der `Ingress` Tabelle:
 
		public static void ReadTable(
		    [Table("Ingress")] IQueryable<Person> tableBinding,
		    TextWriter logger)
		{
		    var query = from p in tableBinding select p;
		    foreach (Person person in query)
		    {
		        logger.WriteLine("PK:{0}, RK:{1}, Name:{2}", 
		            person.PartitionKey, person.RowKey, person.Name);
		    }
		}

### <a id="readone"></a> Gewusst wie: Lesen eine einzelne Entität aus einer Tabelle

Es ist ein `Table` Attributkonstruktor mit zwei zusätzliche Parameter, mit denen Sie den Partitionsschlüssel und Zeilenschlüssel angeben, wenn Sie eine einzelne Tabellenentität binden möchten.

Das folgende Codebeispiel liest eine Tabellenzeile für eine `Person` -Entität anhand der Partition und Zeilenschlüssel Schlüsselwerte in einer Warteschlange erhalten haben:

		public static void ReadTableEntity(
		    [QueueTrigger("inputqueue")] Person personInQueue,
		    [Table("persontable","{PartitionKey}", "{RowKey}")] Person personInTable,
		    TextWriter logger)
		{
		    if (personInTable == null)
		    {
		        logger.WriteLine("Person not found: PK:{0}, RK:{1}",
		                personInQueue.PartitionKey, personInQueue.RowKey);
		    }
		    else
		    {
		        logger.WriteLine("Person found: PK:{0}, RK:{1}, Name:{2}",
		                personInTable.PartitionKey, personInTable.RowKey, personInTable.Name);
		    }
		}


Die `Person` -Klasse in diesem Beispiel keine Implementierung `ITableEntity`.

## <a id="storageapi"></a> Wie Sie die .NET-API direkt zum Arbeiten mit einer Tabelle

Können Sie auch die `Table` -Attribut mit einem `CloudTable` -Objekt für mehr Flexibilität bei der Arbeit mit einer Tabelle.

Im folgenden Codebeispiel wird ein `CloudTable` -Objekt, das eine einzelne Entität zum Hinzufügen der *eingehend* Tabelle.
 
		public static void UseStorageAPI(
		    [Table("Ingress")] CloudTable tableBinding,
		    TextWriter logger)
		{
		    var person = new Person()
		        {
		            PartitionKey = "Test",
		            RowKey = "100",
		            Name = "Name"
		        };
		    TableOperation insertOperation = TableOperation.Insert(person);
		    tableBinding.Execute(insertOperation);
		}

Weitere Informationen zur Verwendung der `CloudTable` Objekt, finden Sie unter [zum Verwenden von Table Storage from .NET](../storage-dotnet-how-to-use-tables.md).

## <a id="queues"></a>Verwandte Themen durch Warteschlangen Gewusst-wie-Artikel

Informationen zur Verarbeitung Tabelle durch eine warteschlangennachricht ausgelöst oder WebJobs-SDK-Szenarien, die nicht spezifisch für die Tabellenverarbeitung finden Sie in [Verwendung von Azure-Warteschlangenspeicher mit dem WebJobs SDK](websites-dotnet-webjobs-sdk-storage-queues-how-to.md).

In diesem Artikel werden u. a. die folgenden Themen behandelt:

* Asynchrone Funktionen
* Mehrere Instanzen
* Ordnungsgemäßes Herunterfahren
* Verwenden von Webaufträge-SDK-Attributen im Hauptteil einer Funktion
* Festlegen der SDK-Verbindungszeichenfolgen im Code
* Festlegen von Werten für WebJobs SDK-Konstruktorparametern im Code
* Manuelles Auslösen einer Funktion
* Schreiben von Protokollen

## <a id="nextsteps"></a> Nächste Schritte

In dieser Anleitung wurden Codebeispiele bereitgestellt, in denen veranschaulicht wird, wie häufige Szenarien für das Arbeiten mit Azure-Tabellen behandelt werden. Weitere Informationen zur Verwendung von Azure-WebJobs und das WebJobs-SDK finden Sie unter [Azure WebJobs empfohlene Ressourcen](http://go.microsoft.com/fwlink/?linkid=390226).
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->