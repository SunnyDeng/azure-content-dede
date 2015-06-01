<properties 
    pageTitle="Verwenden des Warteschlangenspeichers (C++) | Microsoft Azure" 
    description="Erfahren Sie, wie Sie den Warteschlangenspeicher-Dienst in Azure verwenden. Die Beispiele sind in C++ geschrieben." 
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

# Verwenden des Warteschlangenspeichers mit C++  

[AZURE.INCLUDE [storage-selector-queue-include](../includes/storage-selector-queue-include.md)]

## Übersicht
In diesem Leitfaden wird die Durchführung häufiger Szenarien mit dem Azure-Warteschlangen-Speicherdienst demonstriert. Die Beispiele sind in C++ geschrieben und greifen auf die [Azure-Speicherclientbibliothek für C++](https://github.com/Azure/azure-storage-cpp/blob/v0.5.0-preview/README.md) zurück. Zu den Szenarien gehören das **Einfügen**, **Einsehen**, **Abrufen** und **Löschen** von Warteschlangennachrichten sowie das **Erstellen und Löschen von Warteschlangen**.

>[AZURE.NOTE]Diese Anleitung gilt für die Azure-Speicherclientbibliothek für C++ in der Version 0.5.0 und höher. Die empfohlene Version ist Speicherclientbibliothek 0.5.0, die über [NuGet](http://www.nuget.org/packages/wastorage) oder [GitHub](https://github.com/) verfügbar ist.

[AZURE.INCLUDE [storage-queue-concepts-include](../includes/storage-queue-concepts-include.md)]
[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]

## Erstellen einer C++-Anwendung  
In diesem Handbuch werden Sie Speicherfunktionen verwenden, die innerhalb einer C++-Anwendung ausgeführt werden können.

Dafür müssen Sie die Azure-Speicherclientbibliothek für C++ installieren und ein Azure-Speicherkonto in Ihrem Azure-Abonnement erstellen.

Zum Installieren der Azure-Speicherclientbibliothek für C++ können Sie die folgenden Methoden verwenden:

-	**Linux:** Befolgen Sie die Anweisungen auf der Seite [Azure Storage Client Library for C++ README](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) (in englischer Sprache).  
-	**Windows:** Klicken Sie in Visual Studio auf **Extras > NuGet-Paket-Manager > Paket-Manager-Konsole**. Geben Sie im Fenster der [NuGet-Paket-Manager-Konsole](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) den folgenden Befehl ein, und drücken Sie die **EINGABETASTE**:  

		Install-Package wastorage -Pre  
 
## Konfigurieren Ihrer Anwendung für den Zugriff auf den Warteschlangenspeicher
Fügen Sie folgende "include"-Anweisungen am Anfang der C++-Datei dort ein, wo Azure-Speicher-APIs auf Warteschlangen zugreifen sollen:

	#include "was/storage_account.h"
	#include "was/queue.h"

## Einrichten einer Azure-Speicherverbindungszeichenfolge

Ein Azure-Speicherclient verwendet eine Speicherverbindungszeichenfolge zum Speichern von Endpunkten und Anmeldeinformationen für den Zugriff auf Datenverwaltungsdienste. Bei der Ausführung in einer Clientanwendung muss die Speicherverbindungszeichenfolge in dem unten gezeigten Format angegeben werden. Dabei müssen der Name Ihres Speicherkontos und der primäre Zugriffsschlüssel für das im Verwaltungsportal aufgeführte Speicherkonto als *AccountName*- und *AccountKey*-Werte eingegeben werden. Weitere Informationen über Speicherkonten und Zugriffsschlüssel finden Sie unter [Informationen zu Azure-Speicherkonten](storage-create-storage-account.md). Dieses Beispiel zeigt, wie Sie ein statisches Feld für die Verbindungszeichenfolge deklarieren:

	// Define the connection-string with your values.
	const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));

Zum Testen der Anwendung auf Ihrem lokalen Windows-Computer können Sie den [Microsoft Azure-Speicheremulator](https://msdn.microsoft.com/library/azure/hh403989.aspx) verwenden, der mit dem [Azure SDK](http://azure.microsoft.com/downloads/) installiert wird. Der Speicheremulator ist ein Dienstprogramm, das die in Azure verfügbaren Blob-, Warteschlangen- und Tabellen-Dienste auf dem lokalen Entwicklungscomputer simuliert. Im folgenden Beispiel wird dargestellt, wie Sie ein statisches Feld zur Übergabe der Verbindungszeichenfolge an den lokalen Speicheremulator deklarieren:

	// Define the connection-string with Azure Storage Emulator.
	const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  

Wählen Sie zum Starten des Azure-Speicheremulators die Schaltfläche **Start** aus, oder drücken Sie die **Windows**-Taste. Geben Sie **Azure-Speicheremulator** ein, und wählen Sie den **Microsoft Azure-Speicheremulator** aus der Liste der Anwendungen aus.

In den folgenden Beispielen wird davon ausgegangen, dass Sie eine dieser zwei Methoden verwendet haben, um die Speicherverbindungszeichenfolge abzurufen.

## Abrufen der Verbindungszeichenfolge
Sie können Ihre Speicherkontoinformationen mit der Klasse **cloud_storage_account**darstellen. Verwenden Sie zum Abrufen von Speicherkontoinformationen aus der Speicher-Verbindungszeichenfolge die **parse**-Methode.

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

## Erstellen einer Warteschlange
Mit einem **cloud_queue_client**-Objekt können Sie Referenzobjekte für Warteschlangen abrufen. Mit dem folgenden Code wird ein **cloud_queue_client**-Objekt erstellt.

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create a queue client.
	azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

Mithilfe des **cloud_queue_client**-Objekts können Sie einen Verweis auf die Warteschlange abrufen, die Sie verwenden möchten. Sie können die Warteschlange erstellen, wenn sie nicht vorhanden ist.

	// Retrieve a reference to a queue.
	azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

	// Create the queue if it doesn't already exist.
	queue.create_if_not_exists();

## Einfügen einer Nachricht in eine Warteschlange
Um eine Nachricht in eine vorhandene Warteschlange einzufügen, erstellen Sie zuerst eine neue **cloud_queue_message**. Rufen Sie dann die Methode **add_message** auf. Die **cloud_queue_message** kann entweder aus einer Zeichenfolge oder aus einem **Byte**-Array erstellt werden. Dieser Code erstellte eine Warteschlange (wenn sie noch nicht vorhanden ist) und fügt die Nachricht "Hello, World" ein:

	// Retrieve storage account from connection-string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the queue client.
	azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

	// Retrieve a reference to a queue.
	azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

	// Create the queue if it doesn't already exist.
	queue.create_if_not_exists();

	// Create a message and add it to the queue.
	azure::storage::cloud_queue_message message1(U("Hello, World"));
	queue.add_message(message1);  

## Einsehen der nächsten Nachricht
Sie können einen Blick auf die Nachricht am Anfang einer Warteschlange werfen, ohne sie aus der Warteschlange zu entfernen, indem Sie die **peek_message**-Methode aufrufen.

	// Retrieve storage account from connection-string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the queue client.
	azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

	// Retrieve a reference to a queue.
	azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

	// Peek at the next message.
	azure::storage::cloud_queue_message peeked_message = queue.peek_message();

	// Output the message value.
	std::wcout << U("Peeked message content: ") << peeked_message.content_as_string() << std::endl;

## Ändern des Inhalts von Nachrichten in der Warteschlange
Sie können den Inhalt einer Nachricht vor Ort in der Warteschlange ändern. Wenn die Nachricht eine Arbeitsaufgabe darstellt, können Sie diese Funktion verwenden, um den Status der Aufgabe zu aktualisieren. Mit dem folgenden Code wird die Warteschlangennachricht mit neuem Inhalt aktualisiert und das Sichtbarkeits-Zeitlimit um weitere 60 Sekunden verlängert. Dadurch wird der mit der Nachricht verknüpfte Arbeitsstatus gespeichert, und der Client erhält eine weitere Minute zur Bearbeitung der Nachricht. Sie können diese Technik verwenden, um Workflows mit mehreren Schritten in Warteschlangennachrichten zu verfolgen, ohne von vorn beginnen zu müssen, wenn ein Verarbeitungsschritt aufgrund eines Hardware- oder Softwarefehlers fehlschlägt. In der Regel behalten Sie auch die Anzahl der Wiederholungen bei, und wenn die Nachricht mehr als n Mal wiederholt wurde, wird sie gelöscht. Dies verhindert, dass eine Nachricht bei jeder Verarbeitung einen Anwendungsfehler auslöst.

	// Retrieve storage account from connection-string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_conection_string);

	// Create the queue client.
	azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

	// Retrieve a reference to a queue.
	azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));
		
	// Get the message from the queue and update the message contents.
	// The visibility timeout "0" means make it visible immediately.
	// The visibility timeout "60" means the client can get another minute to continue 
	// working on the message.
	azure::storage::cloud_queue_message changed_message = queue.get_message();

	changed_message.set_content(U("Changed message"));
	queue.update_message(changed_message, std::chrono::seconds(60), true);
	std::wcout << U("Changed message content: ") << changed_message.content_as_string() << std::endl;  

## Entfernen aus der Warteschlange bei nächster Nachricht
Dieser Code entfernt eine Nachricht in zwei Schritten aus der Warteschlange. Wenn Sie **get_message** aufrufen, wird die nächste Nachricht aus der Warteschlange abgerufen. Die für **get_message** zurückgegebene Nachricht ist für anderen Code nicht mehr sichtbar, der Nachrichten aus dieser Warteschlange liest. Um die Nachricht endgültig aus der Warteschlange zu entfernen, müssen Sie außerdem **delete_message** aufrufen. Dieser zweistufige Prozess zum Entfernen von Nachrichten stellt sicher, dass eine andere Codeinstanz dieselbe Nachricht erneut abrufen kann, falls die Verarbeitung aufgrund eines Hardware- oder Softwarefehlers fehlschlägt. Der Code ruft **delete_message** direkt nach der Verarbeitung der Nachricht auf.

	// Retrieve storage account from connection-string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the queue client.
	azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

	// Retrieve a reference to a queue.
	azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

	// Get the next message.
	azure::storage::cloud_queue_message dequeued_message = queue.get_message();
	std::wcout << U("Dequeued message: ") << dequeued_message.content_as_string() << std::endl;

	// Delete the message.
	queue.delete_message(dequeued_message);  


## Nutzen zusätzlicher Optionen für das Entfernen von Nachrichten aus der Warteschlange
Es gibt zwei Möglichkeiten, wie Sie das Abrufen von Nachrichten aus der Warteschlange anpassen können. Erstens können Sie einen Nachrichtenstapel abrufen (bis zu 32). Zweitens können Sie das Unsichtbarkeits-Zeitlimit verkürzen oder verlängern, sodass der Code mehr oder weniger Zeit zur vollständigen Verarbeitung jeder Nachricht benötigt. Das folgende Codebeispiel verwendet die **get_messages**-Methode, um 20 Nachrichten mit einem Aufruf abzurufen. Anschließend wird jede Nachricht mithilfe einer **for**-Schleife verarbeitet. Außerdem wird das Unsichtbarkeits-Zeitlimit auf fünf Minuten pro Nachricht festgelegt. Beachten Sie, dass die 5 Minuten für alle Nachrichten gleichzeitig beginnen, sodass 5 Minuten nach dem Aufruf von **get_messages** alle Nachrichten, die nicht gelöscht wurden, wieder sichtbar werden.

	// Retrieve storage account from connection-string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the queue client.
	azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

	// Retrieve a reference to a queue.
	azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

	// Dequeue some queue messages (maximum 32 at a time) and set their visibility timeout to 
	// 5 minutes (300 seconds).
	azure::storage::queue_request_options options;
	azure::storage::operation_context context;

	// Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
	std::vector<azure::storage::cloud_queue_message> messages = queue.get_messages(20, std::chrono::seconds(300), options, context);
		
	std::vector<azure::storage::cloud_queue_message>::const_iterator i;

	for (i = messages.cbegin(); i != messages.cend(); ++i)
	{
    	// Display the contents of the message.
    	std::wcout << U("Get: ") << i->content_as_string() << std::endl;
	}

## Abrufen der Warteschlangenlänge
Sie können die Anzahl der Nachrichten in einer Warteschlange schätzen lassen. Die **download_attributes**-Methode fordert den Warteschlangendienst auf, die Warteschlangenattribute einschließlich der Nachrichtenanzahl abzurufen. Die **approximate_message_count**-Methode ruft die ungefähre Anzahl der Nachrichten in der Warteschlange ab.

	// Retrieve storage account from connection-string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the queue client.
	azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

	// Retrieve a reference to a queue.
	azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

	// Fetch the queue attributes.
	queue.download_attributes();

	// Retrieve the cached approximate message count.
	int cachedMessageCount = queue.approximate_message_count();

	// Display number of messages.
	std::wcout << U("Number of messages in queue: ") << cachedMessageCount << std::endl;  

## Löschen von Warteschlangen
Zum Löschen einer Warteschlange und aller darin enthaltenen Nachrichten rufen Sie die **delete_queue_if_exists**-Methode für das Warteschlangenobjekt auf.

	// Retrieve storage account from connection-string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the queue client.
	azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

	// Retrieve a reference to a queue.
	azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

	// If the queue exists and delete it.
	queue.delete_queue_if_exists();  

## Nächste Schritte
Nachdem Sie sich nun mit den Grundlagen von Warteschlangenspeichern vertraut gemacht haben, lesen Sie die folgenden Artikel, um mehr über Azure-Speicher zu erfahren.

-	[Verwenden des Blob-Speichers mit C++](storage-c-plus-plus-how-to-use-blobs.md)
-	[Verwenden des Tabellenspeichers mit C++](storage-c-plus-plus-how-to-use-tables.md)
-	[Speicherclientbibliothek für C++](https://msdn.microsoft.com/library/azure/gg433040.aspx) 
-	[MSDN-Referenz für Azure-Speicher](https://msdn.microsoft.com/library/azure/gg433040.aspx)
-	[Azure-Speicherdokumentation](http://azure.microsoft.com/documentation/services/storage/)


<!--HONumber=52-->
