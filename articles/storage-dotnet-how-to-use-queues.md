<properties urlDisplayName="Queue Service" pageTitle="Verwenden des Warteschlangenspeichers mit .NET | Microsoft Azure" metaKeywords="Get started Azure queue   Azure asynchronous processing   Azure queue   Azure queue storage   Azure queue .NET   Azure queue storage .NET   Azure queue C#   Azure queue storage C#" description="Erfahren Sie, wie Sie den Microsoft Azure-Warteschlangenspeicher zum Erstellen und L&ouml;schen von Warteschlangen sowie zum Einf&uuml;gen, Einsehen, Abrufen und L&ouml;schen von Warteschlangennachrichten verwenden." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="Verwenden des Microsoft Azure-Warteschlangenspeichers" authors="tamram" manager="adinah" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# Verwenden des Warteschlangenspeichers mit .NET

Diese Anleitung beschreibt die Ausführung gängiger Szenarien mithilfe des
Azure-Warteschlangenspeicherdiensts. Die Beispiele sind in C# geschrieben
und verwenden den Azure-Speicherclient für .NET. Zu den Szenarien gehören das **Einfügen**,
**Einsehen**,
**Abrufen** und
**Löschen** von Warteschlangennachrichten sowie das
**Erstellen und Löschen von Warteschlangen**. Weitere Informationen zu Warteschlange finden
Sie im Abschnitt [nächste Schritte][nächste Schritte].

> [WACOM.NOTE] Diese Anleitung gilt für die Azure Speicher-Clientbibliothek 2.x und neuere Versionen. Die empfohlene Version für die Speicher-Clientbibliothek ist 4.x. Diese Version ist entweder über [NuGet][NuGet] oder als Teil des [Azure SDK für .NET][Azure SDK für .NET] erhältlich. Siehe [Gewusst wie: Programmgesteuerter Zugriff auf den Warteschlangenspeicher][Gewusst wie: Programmgesteuerter Zugriff auf den Warteschlangenspeicher] weiter unten für weitere Informationen zum Download der Speicher-Clientbibliothek.

## Inhaltsverzeichnis

-   [Was ist Warteschlangenspeicherung?][Was ist Warteschlangenspeicherung?]
-   [Konzepte][Konzepte]
-   [Erstellen eines Azure-Speicherkontos][Erstellen eines Azure-Speicherkontos]
-   [Einrichten einer Azure-Speicherverbindungszeichenfolge][Einrichten einer Azure-Speicherverbindungszeichenfolge]
-   [Gewusst wie: Programmgesteuerter Zugriff auf den Warteschlangenspeicher][Gewusst wie: Programmgesteuerter Zugriff auf den Warteschlangenspeicher]
-   [Gewusst wie: Erstellen einer Warteschlange][Gewusst wie: Erstellen einer Warteschlange]
-   [Gewusst wie: Einfügen einer Nachricht in eine Warteschlange][Gewusst wie: Einfügen einer Nachricht in eine Warteschlange]
-   [Gewusst wie: Einsehen der nächsten Nachricht][Gewusst wie: Einsehen der nächsten Nachricht]
-   [Gewusst wie: Ändern des Inhalts von Nachrichten in der Warteschlange][Gewusst wie: Ändern des Inhalts von Nachrichten in der Warteschlange]
-   [Gewusst wie: Entfernen der nächsten Nachricht aus der Warteschlange][Gewusst wie: Entfernen der nächsten Nachricht aus der Warteschlange]
-   [Gewusst wie: Nutzen zusätzlicher Optionen für das Entfernen von Nachrichten aus der Warteschlange][Gewusst wie: Nutzen zusätzlicher Optionen für das Entfernen von Nachrichten aus der Warteschlange]
-   [Gewusst wie: Abrufen der Warteschlangenlänge][Gewusst wie: Abrufen der Warteschlangenlänge]
-   [Gewusst wie: Löschen einer Warteschlange][Gewusst wie: Löschen einer Warteschlange]
-   [Nächste Schritte][nächste Schritte]

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

## 

## <a name="create-account"></a><span class="short-header">Erstellen eines Kontos</span>Erstellen eines Azure-Speicherkontos

</h2>
[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## 

## <a name="setup-connection-string"></a><span class="short-header">Einrichten einer Verbindungszeichenfolge</span>Einrichten einer Azure-Speicherverbindungszeichenfolge

</h2>
[WACOM.INCLUDE [storage-configure-connection-string](../includes/storage-configure-connection-string.md)]

## <a name="configure-access"> </a><span class="short-header">Programmgesteuerter Zugriff</span>Gewusst wie: Programmgesteuerter Zugriff auf den Warteschlangenspeicher

### Abrufen der Assembly

Sie können die Assembly`Microsoft.WindowsAzure.Storage.dll` mit NuGet abrufen. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Manage NuGet Packages** aus. Suchen Sie online nach "WindowsAzure.Storage", und klicken Sie auf **Installieren**, um das Azure-Speicherpaket und die zugehörigen Abhängigkeiten zu installieren.

`Microsoft.WindowsAzure.Storage.dll` ist ebenfalls im Azure SDK für .NET enthalten, das Sie im [.NET Developer Center][.NET Developer Center] herunterladen können. Die Assembly wird im Verzeichnis `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\`lt;sdk-version\>\\ref\\</code> installiert.

### Namespace-Deklarationen

Fügen Sie die folgenden Namespace-Deklarationen am Anfang aller C#-Dateien hinzu,
in denen Sie auf den Azure-Speicher zugreifen möchten:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;

Stellen Sie sicher, dass auf die `Microsoft.WindowsAzure.Storage.dll`-Assembly verwiesen wird.

### Abrufen der Verbindungszeichenfolge

Sie können den Typ **CloudStorageAccount** verwenden, um Ihre
Speicherkontodaten abzubilden. Falls Sie eine Windows
Azure-Projektvorlage verwenden und/oder einen Verweis auf den
Microsoft.WindowsAzure.CloudConfigurationManager haben, können Sie
den Typ **CloudConfigurationManager** verwenden,
um Ihre Speicherverbindungszeichenfolge und
Speicherkontodaten aus der Azure-Dienstkonfiguration abzurufen:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Wenn Sie eine Anwendung ohne einen Verweis auf Microsoft.WindowsAzure.CloudConfigurationManager"stellen und die Verbindungszeichenfolge sich in `web.config` oder `app.config` befindet, wie oben gezeigt, können Sie die Verbindungszeichenfolge mit **ConfigurationManager** abrufen. Sie müssen einen Verweis auf "System.Configuration.dll" zu Ihrem Projekt und eine andere Namespace-Deklaration dafür hinzufügen:

    using System.Configuration;
    ...
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

### ODataLib-Abhängigkeiten

ODataLib-Abhängigkeiten in der Speicherclientbibliothek für .NET werden durch die ODataLib-Pakete (Version 5.0.2), die über NuGet verfügbar sind, und nicht durch WCF Data Services aufgelöst. Die ODataLib-Bibliotheken können direkt heruntergeladen werden, oder es wird über Ihr Codeprojekt durch NuGet darauf verwiesen. Die spezifischen ODataLib-Pakete sind [OData][OData], [Edm][Edm] und [Spatial][Spatial].

## <a name="create-queue"></a><span class="short-header">Erstellen einer Warteschlange</span>Gewusst wie: Erstellen einer Warteschlange

Mit einem **CloudQueueClient**-Objekt können Sie Referenzobjekte für Warteschlangen abrufen.
Der folgende Code erstellt ein **CloudQueueClient**-Objekt. Der Code in dieser Anleitung setzt voraus,
dass die Speicher-Verbindungszeichenfolge in der Dienstkonfiguration
der Azure-Anwendung gespeichert ist. Es gibt noch andere Möglichkeiten,
ein **CloudStorageAccount**-Objekt zu erstellen. Ausführliche Informationen dazu finden Sie in der [CloudStorageAccount][CloudStorageAccount]
-Dokumentation.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

Verwenden Sie das **queueClient**-Objekt, um einen Verweis auf die gewünschte
Warteschlange abzurufen. Sie können die Warteschlange erstellen, wenn sie nicht vorhanden ist.

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExists();

## <a name="insert-message"> </a><span class="short-header">Einfügen einer Nachricht</span>Gewusst wie: Einfügen einer Nachricht in eine Warteschlange

Um eine Nachricht in eine vorhandene Warteschlange einzufügen, erstellen Sie zuerst ein neues
**CloudQueueMessage**-Objekt. Anschließend rufen Sie die **AddMessage**-Methode auf. Sie können eine
**CloudQueueMessage** entweder aus einer Zeichenfolge (im UTF-8-
Format) oder aus einem **Byte**-Array erstellen. Dieser Code erstellt die Warteschlange (falls sie
nicht existiert) und fügt die Nachricht 'Hello, World' ein:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.CreateIfNotExists();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.AddMessage(message);

## <a name="peek-message"></a><span class="short-header">Einsehen der nächsten Nachricht</span>Gewusst wie: Einsehen der nächsten Nachricht

Sie können die Nachricht am Anfang einer Warteschlange einsehen, ohne sie
aus der Warteschlange zu entfernen, indem Sie die Meldung **PeekMessage** aufrufen.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Peek at the next message
    CloudQueueMessage peekedMessage = queue.PeekMessage();

    // Display message.
    Console.WriteLine(peekedMessage.AsString);

## <a name="change-contents"></a><span class="short-header">Ändern des Inhalts einer Nachricht</span>Gewusst wie: Ändern des Inhalts von Nachrichten in der Warteschlange

Sie können den Inhalt einer Nachricht vor Ort in der Warteschlange ändern. Wenn die
Nachricht eine Arbeitsaufgabe darstellt, können Sie diese Funktion verwenden,
um den Status der Aufgabe zu aktualisieren. Der folgende Code aktualisiert die Warteschlangennachricht
mit neuen Inhalten und verlängert die Sichtbarkeitsdauer um weitere 60
Sekunden. Auf diese Weise wird der Status der Aufgabe gespeichert, auf die sich die Nachricht bezieht,
und der Client erhält eine weitere Minute für die Bearbeitung der Nachricht. Mit
dieser Methode können Sie z. B. mehrteilige Workflows auf Warteschlangennachrichten
verwalten, ohne im Fall eines Hardware- oder Softwarefehlers
von neuem anfangen zu müssen. Normalerweise
würden Sie auch die Anzahl der Versuche speichern und die Nachricht
löschen, wenn deren Verarbeitung *n* mal versucht wurde. So schützen Sie sich vor Nachrichten,
die bei jedem Verarbeitungsversuch einen Anwendungsfehler verursachen.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Get the message from the queue and update the message contents.
    CloudQueueMessage message = queue.GetMessage();
    message.SetMessageContent("Updated contents.") ;
    queue.UpdateMessage(message, 
        TimeSpan.FromSeconds(0.0),  // Make it visible immediately.
        MessageUpdateFields.Content | MessageUpdateFields.Visibility);

## <a name="get-message"></a><span class="short-header">Entfernen der nächsten Nachricht aus der Warteschlange</span>Gewusst wie: Entfernen der nächsten Nachricht aus der Warteschlange

Dieser Code entfernt eine Nachricht in zwei Schritten aus der Warteschlange. Wenn Sie
**GetMessage** aufrufen, wird die nächste Nachricht aus der Warteschlange abgerufen. Die von
**GetMessage** zurückgegebenen Nachrichten werden für anderen Code,
der aus dieser Warteschlange liest, unsichtbar. Standardmäßig bleiben die Nachrichten 30 Sekunden
lang unsichtbar. Sie müssen außerdem
DeleteMessage **aufrufen, um die Nachricht aus der**Warteschlange zu löschen. Dieser zweistufige Prozess beim Löschen einer Nachricht
stellt sicher, dass eine andere Instanz Ihres Codes die Nachricht im
Fall eines Hardware- oder Softwarefehlers erneut abrufen
und verarbeiten kann. Ihr Code ruft **DeleteMessage** direkt nach der Verarbeitung
der Nachricht auf.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Get the next message
    CloudQueueMessage retrievedMessage = queue.GetMessage();

    //Process the message in less than 30 seconds, and then delete the message
    queue.DeleteMessage(retrievedMessage);

## <a name="advanced-get"></a><span class="short-header">Weitere Optionen für das Entfernen aus der Warteschlange</span>Gewusst wie: Nutzen zusätzlicher Optionen für das Entfernen von Nachrichten aus der Warteschlange

Es gibt zwei Möglichkeiten, wie Sie das Abrufen von Nachrichten aus der Warteschlange anpassen können.
Erstens können Sie einen Nachrichtenstapel abrufen (bis zu 32). Zweitens können Sie das
Unsichtbarkeits-Zeitlimit verkürzen oder verlängern, sodass der Code mehr oder
weniger Zeit zur vollständigen Verarbeitung jeder Nachricht benötigt. Im folgenden Codebeispiel wird
**GetMessages** verwendet, um 20 Nachrichten mit einem Aufruf abzurufen. Anschließend werden die
Nachrichten in einer **foreach**-Schleife verarbeitet. Außerdem wird der Unsichtbarkeitstimeout
für jede Nachricht auf fünf Minuten gesetzt. Die 5 Minuten beginnen
für alle Nachrichten gleichzeitig. 5 Minuten nach dem
Aufruf an **GetMessages** werden also alle Nachrichten, die noch nicht gelöscht wurden,
wieder sichtbar.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.
        queue.DeleteMessage(message);
    }

## <a name="get-queue-length"></a><span class="short-header">Abrufen der Warteschlangenlänge</span>Gewusst wie: Abrufen der Warteschlangenlänge

Sie können die Anzahl der Nachrichten in einer Warteschlange schätzen lassen. Die
**FetchAttributes**-Methode ruft die Warteschlangenattribute inklusive
der Nachrichtenanzahl vom Warteschlangendienst ab. Die Eigenschaft **ApproximateMethodCount**
 gibt den letzten von der Methode
**FetchAttributes** abgerufenen Wert zurück, ohne den Warteschlangendienst aufzurufen.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Fetch the queue attributes.
    queue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = queue.ApproximateMessageCount;

    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="delete-queue"></a><span class="short-header">Löschen einer Warteschlange</span>Gewusst wie: Löschen einer Warteschlange

Zum Löschen einer Warteschlange und aller darin enthaltenen Nachrichten rufen Sie die Methode
**Delete** für das Warteschlangenobjekt auf.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Delete the queue.
    queue.Delete();

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen des Warteschlangenspeichers vertraut gemacht haben,
folgen Sie diesen Links, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.

-   Vollständige Informationen zu verfügbaren APIs finden Sie in der Warteschlangendienst-Referenzdokumentation:
    -   [Referenz zur Speicherclientbibliothek für .NET][Referenz zur Speicherclientbibliothek für .NET]
    -   [REST-API-Referenz][REST-API-Referenz]
-   Weitere Informationen zu fortgeschrittenen Aufgaben mit Azure Storage finden Sie unter [Speicher][Speicher].
-   Informationen zum Arbeiten mit dem Azure-Speicher in Back-End-Prozessen für Azure-Webseiten finden Sie unter [Erste Schritte mit dem Azure WebJobs SDK][Erste Schritte mit dem Azure WebJobs SDK].
-   Weitere Informationen zu zusätzlichen Optionen für das Speichern von Daten in Azure finden Sie in den anderen Featureleitfäden.
    -   Verwenden Sie [Tabellenspeicher][Tabellenspeicher] zum Speichern strukturierter Daten.
    -   Verwenden Sie [Blobspeicher][Blobspeicher] zum Speichern unstrukturierter Daten.
    -   Verwenden Sie eine [SQL-Datenbank][SQL-Datenbank] zum Speichern relationaler Daten.

  [nächste Schritte]: #next-steps
  [NuGet]: https://www.nuget.org/packages/WindowsAzure.Storage/
  [Azure SDK für .NET]: /de-de/downloads/
  [Gewusst wie: Programmgesteuerter Zugriff auf den Warteschlangenspeicher]: #configure-access
  [Was ist Warteschlangenspeicherung?]: #what-is
  [Konzepte]: #concepts
  [Erstellen eines Azure-Speicherkontos]: #create-account
  [Einrichten einer Azure-Speicherverbindungszeichenfolge]: #setup-connection-string
  [Gewusst wie: Erstellen einer Warteschlange]: #create-queue
  [Gewusst wie: Einfügen einer Nachricht in eine Warteschlange]: #insert-message
  [Gewusst wie: Einsehen der nächsten Nachricht]: #peek-message
  [Gewusst wie: Ändern des Inhalts von Nachrichten in der Warteschlange]: #change-contents
  [Gewusst wie: Entfernen der nächsten Nachricht aus der Warteschlange]: #get-message
  [Gewusst wie: Nutzen zusätzlicher Optionen für das Entfernen von Nachrichten aus der Warteschlange]: #advanced-get
  [Gewusst wie: Abrufen der Warteschlangenlänge]: #get-queue-length
  [Gewusst wie: Löschen einer Warteschlange]: #delete-queue
  [.NET Developer Center]: http://www.windowsazure.com/de-de/develop/net/#
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
  [CloudStorageAccount]: http://msdn.microsoft.com/de-de/library/microsoft.windowsazure.cloudstorageaccount_methods.aspx
  [Referenz zur Speicherclientbibliothek für .NET]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [REST-API-Referenz]: http://msdn.microsoft.com/de-de/library/windowsazure/dd179355
  [Speicher]: http://msdn.microsoft.com/de-de/library/windowsazure/gg433040.aspx
  [Erste Schritte mit dem Azure WebJobs SDK]: /de-de/documentation/articles/websites-dotnet-webjobs-sdk-get-started/
  [Tabellenspeicher]: /de-de/documentation/articles/storage-dotnet-how-to-use-tables/
  [Blobspeicher]: /de-de/documentation/articles/storage-dotnet-how-to-use-blobs/
  [SQL-Datenbank]: /de-de/documentation/articles/sql-database-dotnet-how-to-use/
