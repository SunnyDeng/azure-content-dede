<properties urlDisplayName="Queue Service" pageTitle="Verwenden des Warteschlangenspeichers mit .NET | Microsoft Azure" metaKeywords="Get started Azure queue   Azure asynchronous processing   Azure queue   Azure queue storage   Azure queue .NET   Azure queue storage .NET   Azure queue C#   Azure queue storage C#" description="Erfahren Sie, wie Sie den Microsoft Azure-Warteschlangenspeicher zum Erstellen und Löschen von Warteschlangen sowie zum Einfügen, Einsehen, Abrufen und Löschen von Warteschlangennachrichten verwenden." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="How to use Microsoft Azure Queue Storage" authors="tamram" manager="adinah" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/10/2014" ms.author="tamram" />

# Verwenden des Warteschlangenspeichers mit .NET
 In diesem Leitfaden wird die Durchführung häufiger Szenarien mit dem Azure-Warteschlangen-Speicherdienst demonstriert. Die Beispiele sind in C# geschrieben und greifen auf den Azure-Speicherclient für .NET zurück. Die dargestellten Szenarien umfassen das **Einfügen**, **Einsehen**, **Abrufen** und **Löschen** von Warteschlangennachrichten sowie das **Erstellen und Löschen von Warteschlangen**. Weitere Informationen zu Warteschlangen finden Sie im Abschnitt [Nächste Schritte][].

> [WACOM.NOTE] Diese Anleitung gilt für die Azure .NET Storage Client Library 2.x und höher. Die empfohlene Version ist Storage Client Library 4.x, die über [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) oder als Teil des [Azure SDK für .NET](/de-de/downloads/)verfügbar ist. Weitere Informationen zum Abrufen der Storage Client Library finden Sie unter [Vorgehensweise: Programmgesteuerter Zugriff auf den Warteschlangenspeicher][] weiter unten.

<h2>Inhaltsverzeichnis</h2>

-   [Was ist der Warteschlangenspeicher?][]
-   [Konzepte][]
-   [Erstellen eines Azure-Speicherkontos][]
-   [Einrichten einer Azure Storage-Verbindungszeichenfolge][]
-   [Vorgehensweise: Programmgesteuerter Zugriff auf den Warteschlangenspeicher][]
-   [Vorgehensweise: Erstellen einer Warteschlange][]
-   [Vorgehensweise: Einfügen einer Nachricht in eine Warteschlange][]
-   [Vorgehensweise: Einsehen der nächsten Nachricht][]
-   [Vorgehensweise: Ändern des Inhalts von Nachrichten in der Warteschlange][]
-   [Vorgehensweise: Entfernen der nächsten Nachricht aus der Warteschlange][]
-   [Vorgehensweise: Nutzen zusätzlicher Optionen für das Entfernen von Nachrichten aus der Warteschlange][]
-   [Vorgehensweise: Abrufen der Warteschlangenlänge][]
-   [Vorgehensweise: Löschen einer Warteschlange][]
-   [Nächste Schritte][]

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

## <h2><a name="create-account"></a>Erstellen eines Azure-Speicherkontos</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <h2><a name="setup-connection-string"></a>Einrichten einer Azure-Speicherverbindungszeichenfolge</h2>

[WACOM.INCLUDE [storage-configure-connection-string](../includes/storage-configure-connection-string.md)]

## <a name="configure-access"> </a>Vorgehensweise: Programmgesteuerter Zugriff auf den Warteschlangenspeicher

<h3>Abrufen der Assembly</h3>
Mithilfe von NuGet können Sie die `Microsoft.WindowsAzure.Storage.dll`-Assembly abrufen. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf Ihr Projekt, und wählen Sie **NuGet-Pakete verwalten** aus.  Suchen Sie online nach "WindowsAzure.Storage", und klicken Sie auf **Installieren**, um das Azure Storage-Paket und die Abhängigkeiten zu installieren.

`Microsoft.WindowsAzure.Storage.dll` ist auch im Azure SDK für .NET enthalten, das aus dem <a href="http://www.windowsazure.com/de-de/develop/net/#">.NET Developer Center</a>heruntergeladen werden kann. Die Assembly wird im Verzeichnis `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\` installiert.

<h3>Namespace-Deklarationen</h3>
Fügen Sie zu Beginn aller C\#-Dateien,
in denen Sie programmgesteuert auf Azure Storage zugreifen möchten, die folgenden Code-Namespace-Deklarationen hinzu:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Queue;

Stellen Sie sicher, dass auf die `Microsoft.WindowsAzure.Storage.dll`-Assembly verwiesen wird.

<h3>Abrufen der Verbindungszeichenfolge</h3>
Sie können Ihre Speicherkontoinformationen mit dem Typ **CloudStorageAccount** darstellen. Wenn Sie eine Windows Azure-Projektvorlage verwenden und/oder über einen Verweis auf "Microsoft.WindowsAzure.CloudConfigurationManager" verfügen, können Sie Ihre Speicherverbindungszeichenfolge und Speicherkontoinformationen mit dem Typ **CloudConfigurationManager** aus der Azure-Dienstkonfiguration abrufen:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Wenn Sie eine Anwendung ohne einen Verweis auf "Microsoft.WindowsAzure.CloudConfigurationManager" erstellen und sich die Verbindungszeichenfolge in der `web.config` oder der `app.config` Datei befindet, wie oben gezeigt, können Sie **ConfigurationManager** verwenden, um die Verbindungszeichenfolge abzurufen.  Sie müssen einen Verweis auf "System.Configuration.dll" zu Ihrem Projekt und eine andere Namespace-Deklaration dafür hinzufügen:

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

<h3>ODataLib-Abhängigkeiten</h3>
ODataLib-Abhängigkeiten in der Speicherclientbibliothek für .NET werden durch die ODataLib-Pakete (Version 5.0.2), die über NuGet verfügbar sind, und nicht durch WCF Data Services aufgelöst.  Die ODataLib-Bibliotheken können direkt heruntergeladen werden, oder es wird über Ihr Codeprojekt durch NuGet darauf verwiesen.  Die spezifischen ODataLib-Pakete sind [OData], [Edm] und [Spatial].

<h2><a name="create-queue"></a>Vorgehensweise: Erstellen einer Warteschlange</h2>

Mit einem **CloudQueueClient**-Objekt können Sie Referenzobjekte für Warteschlangen abrufen. Der folgende Code erstellt ein **CloudQueueClient**-Objekt. Der gesamte Code in diesem Leitfaden verwendet eine in der Dienstkonfiguration der Azure-Anwendung
gespeicherte Speicherverbindungszeichenfolge. Es gibt auch andere Möglichkeiten,
ein **CloudStorageAccount**-Objekt zu erstellen. In der Dokumentation zu [CloudStorageAccount][]
finden Sie Details dazu.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

Use the **queueClient** object to get a reference to the queue you want
to use. You can create the queue if it doesn't exist.

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExists();

<h2><a name="insert-message"> </a>Vorgehensweise: Einfügen einer Nachricht in eine Warteschlange</h2>

Um eine Nachricht in eine vorhandene Warteschlange einzufügen, erstellen Sie zuerst ein neues
**CloudQueueMessage**-Objekt. Rufen Sie dann die Methode **AddMessage** auf. Ein
**CloudQueueMessage**-Objekt kann entweder aus einer Zeichenfolge (im UTF-8-Format)
oder aus einem **Bytearray** erstellt werden. Mit dem folgenden Code wurde eine Warteschlange erstellt (falls
noch nicht vorhanden) und die Nachricht "Hello, World" eingefügt:

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

<h2><a name="peek-message"></a>Vorgehensweise: Einsehen der nächsten Nachricht</h2>

Sie können einen Blick auf die Nachricht am Anfang einer Warteschlange werfen, ohne sie aus der Warteschlange zu entfernen,
indem Sie die Methode **PeekMessage** aufrufen.

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

<h2><a name="change-contents"></a>Vorgehensweise: Ändern des Inhalts von Nachrichten in der Warteschlange</h2>

Sie können den Inhalt einer Nachricht vor Ort in der Warteschlange ändern. Wenn die Nachricht eine Arbeitsaufgabe darstellt, können Sie diese Funktion verwenden, um den Status der Aufgabe zu aktualisieren. Mit dem folgenden Code wird die Warteschlangennachricht mit neuem Inhalt aktualisiert und das Sichtbarkeits-Zeitlimit um weitere 60 Sekunden verlängert. Dadurch wird der mit der Nachricht verknüpfte Arbeitsstatus gespeichert, und der Client erhält eine weitere Minute zur Bearbeitung der Nachricht. Sie können diese Technik verwenden, um Workflows mit mehreren Schritten in Warteschlangennachrichten zu verfolgen, ohne von vorn beginnen zu müssen, wenn ein Verarbeitungsschritt aufgrund eines Hardware- oder Softwarefehlers fehlschlägt. In der Regel behalten Sie auch die Anzahl der Wiederholungen bei, und wenn die Nachricht mehr als n-mal wiederholt wurde, wird sie gelöscht. Dies verhindert, dass eine Nachricht bei jeder Verarbeitung einen Anwendungsfehler auslöst.

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

<h2><a name="get-message"></a>Vorgehensweise: Entfernen der nächsten Nachricht aus der Warteschlange</h2>

Dieser Code entfernt eine Nachricht in zwei Schritten aus der Warteschlange. Wenn Sie **GetMessage** aufrufen, wird die nächste Nachricht aus der Warteschlange abgerufen. Eine von **GetMessage** zurückgegebene Nachricht ist für anderen Code nicht mehr sichtbar, der Nachrichten
aus dieser Warteschlange liest. Standardmäßig bleibt die Nachricht 30 Sekunden lang unsichtbar. Um die Nachricht endgültig aus der Warteschlange zu entfernen, müssen Sie außerdem **DeleteMessage** aufrufen. Dieser zweistufige Prozess zum Entfernen von Nachrichten stellt sicher, dass eine andere Codeinstanz dieselbe Nachricht erneut abrufen kann, falls die Verarbeitung aufgrund eines Hardware- oder Softwarefehlers fehlschlägt. Der Code ruft "deleteMessage" direkt nach der Verarbeitung der Nachricht auf.

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

<h2><a name="advanced-get"></a>Vorgehensweise: Nutzen zusätzlicher Optionen für das Entfernen von Nachrichten aus der Warteschlange</h2>

Es gibt zwei Möglichkeiten, wie Sie das Abrufen von Nachrichten aus der Warteschlange anpassen können. Erstens können Sie einen Nachrichtenstapel abrufen (bis zu 32). Zweitens können Sie das Unsichtbarkeits-Zeitlimit verkürzen oder verlängern, sodass der Code mehr oder weniger Zeit zur vollständigen Verarbeitung jeder Nachricht benötigt. Im folgenden Codebeispiel wird die Methode **GetMessages** verwendet, um 20 Nachrichten mit einem Aufruf abzurufen. Anschließend wird jede Nachricht mithilfe einer **foreach**-Schleife verarbeitet. Außerdem wird das Unsichtbarkeits-Zeitlimit auf fünf Minuten pro Nachricht festgelegt. Die 5 Minuten beginnen für alle Nachrichten gleichzeitig. 5 Minuten nach dem Aufruf an **GetMessages** werden also alle Nachrichten, die noch nicht gelöscht wurden, wieder sichtbar.

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

<h2><a name="get-queue-length"></a>Vorgehensweise: Abrufen der Warteschlangenlänge</h2>

Sie können die Anzahl der Nachrichten in einer Warteschlange schätzen lassen. Die FetchAttributes-Methode ruft die Warteschlangenattribute inklusive der Nachrichtenanzahl vom Warteschlangendienst ab. Die Eigenschaft **ApproximateMethodCount** gibt den letzten von der Methode **FetchAttributes** abgerufenen Wert zurück, ohne den Warteschlangendienst aufzurufen.

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

<h2><a name="delete-queue"></a>Vorgehensweise: Löschen einer Warteschlange</h2>

Zum Löschen einer Warteschlange und aller darin enthaltenen Nachrichten rufen Sie die Methode **Delete** für das Warteschlangenobjekt auf.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Delete the queue.
    queue.Delete();

<h2><a name="next-steps"></a>Nächste Schritte</h2>

Nachdem Sie sich nun mit den Grundlagen der Warteschlangenspeicherung vertraut gemacht haben, folgen Sie diesen Links
um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.

<ul>
<li>Vollständige Informationen zu verfügbaren APIs finden Sie in der Warteschlangendienst-Referenzdokumentation:
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Referenz zur Speicherclientbibliothek für .NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/de-de/library/windowsazure/dd179355">REST-API-Referenz</a></li>
  </ul>
</li>
<li>Unter <a href="http://msdn.microsoft.com/de-de/library/windowsazure/gg433040.aspx">Speichern und Zugreifen auf Daten in Azure</a>finden Sie weitere Informationen zu erweiterten Aufgaben, die Sie mit Azure Storage durchführen können.</li>
<li>Unter <a href="/de-de/documentation/articles/websites-dotnet-webjobs-sdk-get-started/">Erste Schritte mit dem Azure WebJobs SDK</a>finden Sie weitere Informationen zum Arbeiten mit Azure Storage in Back-End-Prozessen für Azure-Websites.</li>
<li>Weitere Informationen zu zusätzlichen Optionen für das Speichern von Daten in Azure finden Sie in den anderen Featureleitfäden.
  <ul>
    <li>Verwenden von <a href="/de-de/documentation/articles/storage-dotnet-how-to-use-tables/">Table Storage</a> zum Speichern strukturierter Daten</li>
    <li>Verwenden von <a href="/de-de/documentation/articles/storage-dotnet-how-to-use-blobs/">Blob-Speicher</a> zum Speichern unstrukturierter Daten</li>
    <li>Verwenden von <a href="/de-de/documentation/articles/sql-database-dotnet-how-to-use/">SQL-Datenbank</a> zum Speichern relationaler Daten</li>
  </ul>
</li>
</ul>



  [Nächste Schritte]: #next-steps
  [Was ist der Warteschlangenspeicher?]: #what-is
  [Konzepte]: #concepts
  [Erstellen eines Azure-Speicherkontos]: #create-account
  [Einrichten einer Azure Storage-Verbindungszeichenfolge]: #setup-connection-string
  [Vorgehensweise: Programmgesteuerter Zugriff auf den Warteschlangenspeicher]: #configure-access
  [Vorgehensweise: Erstellen einer Warteschlange]: #create-queue
  [Vorgehensweise: Einfügen einer Nachricht in eine Warteschlange]: #insert-message
  [Vorgehensweise: Einsehen der nächsten Nachricht]: #peek-message
  [Vorgehensweise: Ändern des Inhalts von Nachrichten in der Warteschlange]: #change-contents
  [Vorgehensweise: Entfernen der nächsten Nachricht aus der Warteschlange]: #get-message
  [Vorgehensweise: Nutzen zusätzlicher Optionen für das Entfernen von Nachrichten aus der Warteschlange]: #advanced-get
  [Vorgehensweise: Abrufen der Warteschlangenlänge]: #get-queue-length
  [Vorgehensweise: Löschen einer Warteschlange]: #delete-queue
  [Download und Installation des Azure SDK für .NET]: /de-de/develop/net/
  [Referenz zur .NET-Clientbibliothek]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Erstellen eines Azure-Projekts in Visual Studio]: http://msdn.microsoft.com/de-de/library/windowsazure/ee405487.aspx 
  [CloudStorageAccount]: http://msdn.microsoft.com/de-de/library/microsoft.windowsazure.cloudstorageaccount_methods.aspx
  [Speichern von und Zugreifen auf Daten in Azure]: http://msdn.microsoft.com/de-de/library/windowsazure/gg433040.aspx
  [Azure Storage-Teamblog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Konfigurieren von Verbindungszeichenfolgen]: http://msdn.microsoft.com/de-de/library/windowsazure/ee758697.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
<!--HONumber=35.1--> 
