<properties urlDisplayName="Queue Service" pageTitle="Verwenden des Warteschlangenspeichers mit .NET | Microsoft Azure" metaKeywords="Get started Azure queue   Azure asynchronous processing   Azure queue   Azure queue storage   Azure queue .NET   Azure queue storage .NET   Azure queue C#   Azure queue storage C#" description="Learn how to use Microsoft Azure Queue storage to create and delete queues and insert, peek, get, and delete queue messages." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="How to use Microsoft Azure Queue Storage" authors="tamram" manager="adinah" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# Verwenden des Warteschlangenspeichers mit .NET

In diesem Leitfaden wird die Durchführung häufiger Szenarien mit dem
Azure-Warteschlangen-Speicherdienst demonstriert. Die Beispiele sind in C\# geschrieben und
und verwenden den Azure-Speicherclient für .NET. Die aufgeführten Szenarien umfassen das **Einfügen**,
**Einsehen**, **Abrufen** und **Löschen** von Warteschlangennachrichten sowie das
**Erstellen und Löschen von Warteschlangen**. Weitere Informationen zu Warteschlangen finden Sie
im Abschnitt[Nächste Schritte][].

> [WACOM.NOTE] Diese Anleitung gilt für die Azure Speicher-Clientbibliothek 2.x und neuere Versionen. Die empfohlene Version für die Speicher-Clientbibliothek ist 4.x. Diese Version ist entweder über [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) oder als Teil des [Azure SDK für .NET](/de-de/downloads/) erhältlich. Siehe [Gewusst wie: Programmgesteuerter Zugriff auf den Warteschlangenspeicher][] weiter unten für weitere Informationen zum Download der Speicher-Clientbibliothek.

<h2>Inhaltsverzeichnis</h2>

-   [Was ist Warteschlangenspeicherung?][]
-   [Konzepte][]
-   [Erstellen eines Azure-Speicherkontos][]
-   [Einrichten einer Azure-Speicherverbindungszeichenfolge][]
-   [Gewusst wie: Programmgesteuerter Zugriff auf den Warteschlangenspeicher][]
-   [Gewusst wie: Erstellen einer Warteschlange][]
-   [Gewusst wie: Einfügen einer Nachricht in eine Warteschlange][]
-   [Gewusst wie: Einsehen der nächsten Nachricht][]
-   [Gewusst wie: Ändern des Inhalts von Nachrichten in der Warteschlange][]
-   [Gewusst wie: Entfernen der nächsten Nachricht aus der Warteschlange][]
-   [Gewusst wie: Nutzen zusätzlicher Optionen für das Entfernen von Nachrichten aus der Warteschlange][]
-   [Gewusst wie: Abrufen der Warteschlangenlänge][]
-   [Gewusst wie: Löschen einer Warteschlange][]
-   [Nächste Schritte][]

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

## <h2><a name="create-account"></a><span  class="short-header">Erstellen eines Kontos</span>Erstellen eines Azure-Speicherkontos</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <h2><a name="setup-connection-string"></a><span  class="short-header">Einrichten einer Verbindungszeichenfolge</span>Einrichten einer Azure-Speicherverbindungszeichenfolge</h2>

[WACOM.INCLUDE [storage-configure-connection-string](../includes/storage-configure-connection-string.md)]

## <a name="configure-access"> </a><span  class="short-header">Programmgesteuerter Zugriff</span>Gewusst wie: Programmgesteuerter Zugriff auf den Warteschlangenspeicher

<h3>Abrufen der Assembly</h3>
Mithilfe von NuGet können Sie die `Microsoft.WindowsAzure.Storage.dll`-Assembly abrufen. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Manage NuGet Packages** aus.  Suchen Sie online nach "WindowsAzure.Storage", und klicken Sie auf **Installieren**, um das Azure-Speicherpaket und die zugehörigen Abhängigkeiten zu installieren.

`Microsoft.WindowsAzure.Storage.dll` ist auch im Azure SDK für .NET 2.0 enthalten, das aus dem <a href="http://www.windowsazure.com/de-de/develop/net/#">.NET Developer Center</a> heruntergeladen werden kann. Die Assembly wird im Verzeichnis `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\` installiert.

<h3>Namespace-Deklarationen</h3>
Fügen Sie zu Beginn aller C\#-Dateien
, in denen Sie programmgesteuert auf Azure-Speicher zugreifen möchten, die folgenden Namespace-Deklarationen hinzu:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Queue;

Stellen Sie sicher, dass auf die `Microsoft.WindowsAzure.Storage.dll`-Assembly verwiesen wird.

<h3>Abrufen der Verbindungszeichenfolge</h3>
Sie können die **CloudStorageAccount** verwenden, um Ihre 
Speicherkontodaten abzubilden. Falls Sie eine Windows 
Azure-Projektvorlage verwenden und/oder über einen Verweis auf 
Microsoft.WindowsAzure.CloudConfigurationManager verfügen, 
können Sie Ihre Speicherverbindungszeichenfolge und Speicherkontoinformationen mit dem Typ **CloudConfigurationManager**
aus
der Azure-Dienstkonfiguration abrufen:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Wenn Sie eine Anwendung ohne einen Verweis auf "Microsoft.WindowsAzure.CloudConfigurationManager" erstellen und die Verbindungszeichenfolge sich in `web.config` oder `app.config` befindet, wie oben gezeigt, können Sie die Verbindungszeichenfolge mit **ConfigurationManager** abrufen.  Sie müssen einen Verweis auf "System.Configuration.dll" zu Ihrem Projekt und eine andere Namespace-Deklaration dafür hinzufügen:

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

<h3>ODataLib-Abhängigkeiten</h3>
ODataLib-Abhängigkeiten in der Speicherclientbibliothek für .NET werden durch die ODataLib-Pakete (Version 5.0.2), die über NuGet verfügbar sind, und nicht durch WCF Data Services aufgelöst.  Die ODataLib-Bibliotheken können direkt heruntergeladen werden, oder es wird über Ihr Codeprojekt durch NuGet darauf verwiesen.  Die spezifischen ODataLib-Pakete sind [OData], [Edm] und [Spatial].

<h2><a name="create-queue"></a><span  class="short-header">Erstellen einer Warteschlange</span>Gewusst wie: Erstellen einer Warteschlange</h2>

Mit einem **CloudQueueClient**-Objekt können Sie Referenzobjekte für Warteschlangen abrufen.
Der folgende Code erstellt ein **CloudQueueClient**-Objekt. Der gesamte Code in
diesem Leitfaden verwendet eine in der Dienstkonfiguration der Azure-Anwendung
gespeicherte Speicherverbindungszeichenfolge. Es gibt auch andere Möglichkeiten,
ein **CloudStorageAccount**-Objekt zu erstellen. Ausführliche Informationen dazu finden Sie in der [CloudStorageAccount][]
-Dokumentation.

    // Speicherkonto aus Verbindungszeichenfolge abrufen.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Warteschlangenclient erstellen.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

Mithilfe des **queueClient**-Objekts können Sie einen Verweis auf die Warteschlange abrufen, die Sie verwenden
möchten. Sie können die Warteschlange erstellen, wenn sie nicht vorhanden ist.

    // Verweis auf eine Warteschlange abrufen.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Warteschlange erstellen, wenn sie noch nicht vorhanden ist.
    queue.CreateIfNotExists();

<h2><a name="insert-message"> </a><span  class="short-header">Einfügen einer Nachricht</span>Gewusst wie: Einfügen einer Nachricht in eine Warteschlange</h2>

Um eine Nachricht in eine vorhandene Warteschlange einzufügen, erstellen Sie zuerst ein neues
**CloudQueueMessage**-Objekt. Anschließend rufen Sie die **AddMessage**-Methode auf. Die
**CloudQueueMessage** kann entweder aus einer Zeichenfolge (im UTF-8-Format
) oder aus einem **Bytearray** erstellt werden. Dieser Code erstellte eine Warteschlange (falls
noch nicht vorhanden) und fügt die Nachricht "Hello, World" ein:

    // Speicherkonto aus Verbindungszeichenfolge abrufen.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Warteschlangenclient erstellen.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Verweis auf eine Warteschlange abrufen.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Warteschlange erstellen, wenn sie noch nicht vorhanden ist.
    queue.CreateIfNotExists();

    // Nachricht erstellen und zur Warteschlange hinzufügen.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.AddMessage(message);

<h2><a name="peek-message"></a><span  class="short-header">Einsehen der nächsten Nachricht</span>Gewusst wie: Einsehen der nächsten Nachricht</h2>

Sie können einen Blick auf die Nachricht am Anfang einer Warteschlange werfen, ohne sie aus der Warteschlange zu entfernen
, indem Sie die Methode **PeekMessage** aufrufen.

    // Speicherkonto aus Verbindungszeichenfolge abrufen.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Warteschlangenclient erstellen.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Verweis auf eine Warteschlange abrufen.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Nächste Nachricht einsehen.
    CloudQueueMessage peekedMessage = queue.PeekMessage();

	// Nachricht anzeigen.
	Console.WriteLine(peekedMessage.AsString);

<h2><a name="change-contents"></a><span  class="short-header">Ändern des Inhalts einer Nachricht</span>Gewusst wie: Ändern des Inhalts von Nachrichten in der Warteschlange</h2>

Sie können den Inhalt einer Nachricht vor Ort in der Warteschlange ändern. Wenn die
Nachricht eine Arbeitsaufgabe darstellt, können Sie diese Funktion verwenden,
um den Status der Aufgabe zu aktualisieren. Der folgende Code aktualisiert die Warteschlangennachricht
mit neuen Inhalten und verlängert die Sichtbarkeitsdauer um weitere 60
Sekunden. Dadurch wird der mit der Nachricht verknüpfte Arbeitsstatus gespeichert, und
der Client erhält eine weitere Minute zur Bearbeitung der Nachricht. Sie
können diese Technik verwenden, um Workflows mit mehreren Schritten in
Warteschlangennachrichten zu verfolgen, ohne von vorn beginnen zu müssen,
wenn ein Verarbeitungsschritt aufgrund eines Hardware- oder Softwarefehlers fehlschlägt. In der Regel
behalten Sie auch die Anzahl der Wiederholungen bei, und wenn die Nachricht mehr
als n Mal*n* wiederholt wurde, wird sie gelöscht. So schützen Sie sich vor Nachrichten,
die bei jedem Verarbeitungsversuch einen Anwendungsfehler verursachen.

    // Speicherkonto aus Verbindungszeichenfolge abrufen.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Warteschlangenclient erstellen.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Verweis auf eine Warteschlange abrufen.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// Nachricht aus der Warteschlange abrufen und Inhalt ändern.
    CloudQueueMessage message = queue.GetMessage();
    message.SetMessageContent("Updated contents.") ;
    queue.UpdateMessage(message, 
        TimeSpan.FromSeconds(0.0),  // Sofort anzeigen.
        MessageUpdateFields.Content | MessageUpdateFields.Visibility);

<h2><a name="get-message"></a><span  class="short-header">Entfernen der nächsten Nachricht aus der Warteschlange</span>Gewusst wie: Entfernen der nächsten Nachricht aus der Warteschlange</h2>

Dieser Code entfernt eine Nachricht in zwei Schritten aus der Warteschlange. Wenn Sie
**GetMessage** aufrufen, wird die nächste Nachricht aus der Warteschlange abgerufen. Die für
**GetMessage** zurückgegebene Nachricht ist für anderen Code nicht mehr sichtbar, der Nachrichten
aus dieser Warteschlange liest. Standardmäßig bleibt die Nachricht 30 Sekunden lang
unsichtbar. Um die Nachricht endgültig aus der Warteschlange zu entfernen, müssen Sie außerdem
**DeleteMessage**aufrufen. Dieser zweistufige Prozess zum Entfernen von Nachrichten
stellt sicher, dass eine andere Codeinstanz dieselbe Nachricht erneut abrufen kann, falls die Verarbeitung aufgrund eines Hardware-
oder Softwarefehlers fehlschlägt. Eine andere Instanz Ihres Codes kann dieselbe Nachricht
erneut abrufen. Ihr Code ruft **DeleteMessage** direkt nach der Verarbeitung
der Nachricht auf.

    // Speicherkonto aus Verbindungszeichenfolge abrufen.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Warteschlangenclient erstellen.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Verweis auf eine Warteschlange abrufen.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Nächste Nachricht abrufen.
    CloudQueueMessage retrievedMessage = queue.GetMessage();

    // Nachricht in weniger als 30 Sekunden verarbeiten und dann löschen.
    queue.DeleteMessage(retrievedMessage);

<h2><a name="advanced-get"></a><span  class="short-header">Weitere Optionen für das Entfernen aus der Warteschlange</span>Gewusst wie: Nutzen zusätzlicher Optionen für das Entfernen von Nachrichten aus der Warteschlange</h2>

Es gibt zwei Möglichkeiten, wie Sie das Abrufen von Nachrichten aus der Warteschlange anpassen können.
Erstens können Sie einen Nachrichtenstapel abrufen (bis zu 32). Zweitens können Sie das
Unsichtbarkeits-Zeitlimit verkürzen oder verlängern, sodass der Code mehr oder
weniger Zeit zur vollständigen Verarbeitung jeder Nachricht benötigt. Das folgende Beispiel verwendet die
**GetMessages**-Methode, um 20 Nachrichten mit einem Aufruf abzurufen. Anschließend werden die
Nachrichten in einer**foreach**-Schleife verarbeitet. Außerdem wird der Unsichtbarkeitstimeout
für jede Nachricht auf fünf Minuten gesetzt. Beachten Sie, dass die 5 Minuten
für alle Nachrichten gleichzeitig beginnen, sodass 5 Minuten nach
dem Aufruf von **GetMessages** alle Nachrichten, die nicht gelöscht wurden,
wieder sichtbar werden.

    // Speicherkonto aus Verbindungszeichenfolge abrufen.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Warteschlangenclient erstellen.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Verweis auf eine Warteschlange abrufen.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Alle Nachrichten in weniger als 5 Minuten verarbeiten und nach der Verarbeitung löschen.
        queue.DeleteMessage(message);
    }

<h2><a name="get-queue-length"></a><span  class="short-header">Abrufen der Warteschlangenlänge</span>Gewusst wie: Abrufen der Warteschlangenlänge</h2>

Sie können die Anzahl der Nachrichten in einer Warteschlange schätzen lassen. Die
**FetchAttributes**-Methode fordert den Warteschlangendienst auf,
die Warteschlangenattribute einschließlich der Nachrichtenanzahl abzurufen. Die Eigenschaft **ApproximateMethodCount**
gibt den letzten von der Methode
**FetchAttributes**-Methode abgerufenen Wert zurück, ohne den Warteschlangendienst aufzurufen.

    // Speicherkonto aus Verbindungszeichenfolge abrufen.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Warteschlangenclient erstellen.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Verweis auf eine Warteschlange abrufen.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// Warteschlangenattribute abrufen.
	queue.FetchAttributes();

    // Zwischengespeicherte ungefähre Nachrichtenanzahl abrufen.
    int? cachedMessageCount = queue.ApproximateMessageCount;

	// Nachrichtenanzahl anzeigen.
	Console.WriteLine("Nachrichtenanzahl in Warteschlange: " + cachedMessageCount);

<h2><a name="delete-queue"></a><span  class="short-header">Löschen einer Warteschlange</span>Gewusst wie: Löschen einer Warteschlange</h2>

Zum Löschen einer Warteschlange und aller darin enthaltenen Nachrichten rufen Sie die
**Delete**-Methode für das Warteschlangenobjekt auf.

    // Speicherkonto aus Verbindungszeichenfolge abrufen.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Warteschlangenclient erstellen.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Verweis auf eine Warteschlange abrufen.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Warteschlange löschen.
    queue.Delete();

<h2><a name="next-steps"></a>Nächste Schritte</h2>

Nachdem Sie sich nun mit den Grundlagen der Warteschlangenspeicherung vertraut gemacht haben, folgen Sie diesen Links
, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.

<ul>
<li>Vollständige Informationen zu verfügbaren APIs finden Sie in der Warteschlangendienst-Referenzdokumentation:
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Referenz zur Speicherclientbibliothek für .NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/de-de/library/windowsazure/dd179355">REST-API-Referenz</a></li>
  </ul>
</li>
<li>Weitere Informationen zu fortgeschrittenen Aufgaben mit Azure Storage finden Sie unter <a href="http://msdn.microsoft.com/de-de/library/windowsazure/gg433040.aspx">Speichern und Zugreifen auf Daten in Azure</a>.</li>
<li>Informationen zum Arbeiten mit dem Azure-Speicher in Back-End-Prozessen für Azure-Webseiten finden Sie unter <a href="/de-de/documentation/articles/websites-dotnet-webjobs-sdk-get-started/">Erste Schritte mit dem Azure WebJobs SDK</a>.</li>
<li>Weitere Informationen zu zusätzlichen Optionen für das Speichern von Daten in Azure finden Sie in den anderen Featureleitfäden.
  <ul>
    <li>Verwenden Sie <a href="/de-de/documentation/articles/storage-dotnet-how-to-use-tables/">Tabellenspeicher</a> zum Speichern strukturierter Daten.</li>
    <li>Verwenden Sie den <a href="/de-de/documentation/articles/storage-dotnet-how-to-use-blobs/">Blobspeicher</a> zum Speichern unstrukturierter Daten.</li>
    <li>Verwenden Sie eine <a href="/de-de/documentation/articles/sql-database-dotnet-how-to-use/">SQL-Datenbank</a> zum Speichern relationaler Daten.</li>
  </ul>
</li>
</ul>



  [Nächste Schritte]: #next-steps
  [Was ist Warteschlangenspeicherung?]: #what-is
  [Konzepte]: #concepts
  [Erstellen eines Azure-Speicherkontos]: #create-account
  [Einrichten einer Azure-Speicherverbindungszeichenfolge]: #setup-connection-string
  [Gewusst wie: Programmgesteuerter Zugriff auf den Warteschlangenspeicher]: #configure-access
  [Gewusst wie: Erstellen einer Warteschlange]: #create-queue
  [Gewusst wie: Einfügen einer Nachricht in eine Warteschlange]: #insert-message
  [Gewusst wie: Einsehen der nächsten Nachricht]: #peek-message
  [Gewusst wie: Ändern des Inhalts von Nachrichten in der Warteschlange]: #change-contents
  [Gewusst wie: Entfernen der nächsten Nachricht aus der Warteschlange]: #get-message
  [Gewusst wie: Zusätzliche Optionen für das Entfernen von Nachrichten aus der Warteschlange]: #advanced-get
  [Gewusst wie: Abrufen der Warteschlangenlänge]: #get-queue-length
  [Gewusst wie: Löschen einer Warteschlange]: #delete-queue
  [Herunterladen und Installieren von Azure-SDK für .NET.]: /de-de/develop/net/
  [Referenz zur .NET-Clientbibliothek]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Azure-Projekt in Visual Studio erstellen]: http://msdn.microsoft.com/de-de/library/windowsazure/ee405487.aspx 
  [CloudStorageAccount]: http://msdn.microsoft.com/de-de/library/microsoft.windowsazure.cloudstorageaccount_methods.aspx
  [Speichern und Zugreifen auf Daten in Azure]: http://msdn.microsoft.com/de-de/library/windowsazure/gg433040.aspx
  [Blog des Azure-Speicherteams]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configuring Connection Strings]: http://msdn.microsoft.com/de-de/library/windowsazure/ee758697.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
