<properties linkid="dev-net-2-how-to-queue-service" urlDisplayName="Queue Service" pageTitle="How to use queue storage from .NET | Microsoft Azure" metaKeywords="Get started Azure queue   Azure asynchronous processing   Azure queue   Azure queue storage   Azure queue .NET   Azure queue storage .NET   Azure queue C#   Azure queue storage C#" description="Learn how to use the Windows Azure queue storage service to create and delete queues and insert, peek, get, and delete queue messages." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="How to use the Queue Storage Service" authors="tamram" />

Verwenden der Warteschlangenspeicherung von .NET
================================================

In diesem Leitfaden wird die Durchführung häufiger Szenarien mit dem Azure-Warteschlangen-Speicherdienst demonstriert. Die Beispiele sind in C\# geschrieben und greifen auf den Azure-Speicherclient für .NET zurück. Zu den Szenarien gehören das **Einfügen**, **Einsehen**, **Abrufen** und **Löschen** von Warteschlangennachrichten sowie das **Erstellen und Löschen von Warteschlangen**. Weitere Informationen zu Warteschlangen finden Sie im Abschnitt [Nächste Schritte](#next-steps).

Inhaltsverzeichnis
------------------

-   [Was ist Warteschlangenspeicherung?](#what-is)
-   [Konzepte](#concepts)
-   [Erstellen eines Azure-Speicherkontos](#create-account)
-   [Einrichten einer Azure-Speicherverbindungszeichenfolge](#setup-connection-string)
-   [Gewusst wie: Programmgesteuertes Zugreifen auf Warteschlangen mithilfe von .NET](#access)
-   [Gewusst wie: Erstellen einer Warteschlange](#create-queue)
-   [Gewusst wie: Einfügen einer Nachricht in eine Warteschlange](#insert-message)
-   [Gewusst wie: Einsehen der nächsten Nachricht](#peek-message)
-   [Gewusst wie: Ändern des Inhalts von Nachrichten in der Warteschlange](#change-contents)
-   [Gewusst wie: Entfernen der nächsten Nachricht aus der Warteschlange](#get-message)
-   [Gewusst wie: Nutzen zusätzlicher Optionen für das Entfernen von Nachrichten aus der Warteschlange](#advanced-get)
-   [Gewusst wie: Abrufen der Warteschlangenlänge](#get-queue-length)
-   [Gewusst wie: Löschen einer Warteschlange](#delete-queue)
-   [Nächste Schritte](#next-steps)

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

Erstellen eines KontosErstellen eines Azure-Speicherkontos
----------------------------------------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Einrichten einer VerbindungszeichenfolgeEinrichten einer Azure-Speicherverbindungszeichenfolge
----------------------------------------------------------------------------------------------

Die Azure-Speicherclientbibliothek für .NET unterstützt die Verwendung einer Speicherverbindungszeichenfolge zum Konfigurieren von Endpunkten und Anmeldeinformationen für den Zugriff auf Speicherdienste. Sie können die Speicherverbindungszeichenfolge in einer Konfigurationsdatei ablegen, statt sie fest in Code zu programmieren:

-   Bei der Verwendung von Azure-Clouddiensten empfiehlt es sich, die Verbindungszeichenfolge mithilfe des Azure-Dienstkonfigurationssystems (Dateien `*.csdef` und `*.cscfg`) zu speichern.
-   Bei der Verwendung von Azure-Websites und virtuellen Azure-Maschinen oder der Erstellung von .NET-Anwendungen, die nicht in Azure ausgeführt werden sollen, empfiehlt es sich, die Verbindungszeichenfolge mit dem .NET-Konfigurationssystem (z. B. der Datei `web.config` oder `app.config`) zu speichern.

In beiden Fällen können Sie die Verbindungszeichenfolge mit der `CloudConfigurationManager.GetSetting`-Methode abrufen, wie später in diesem Leitfaden gezeigt.

### Konfigurieren der Verbindungszeichenfolge bei der Verwendung von Cloud Services

Die Dienstkonfiguration ist in Azure-Clouddiensten-Projekten eindeutig und ermöglicht es Ihnen, Konfigurationseinstellungen im Azure-Verwaltungsportal dynamisch zu ändern, ohne die Anwendung erneut bereitzustellen.

So konfigurieren Sie die Verbindungszeichenfolge in der Azure-Dienstkonfiguration:

1.  Klicken Sie im Projektmappen-Explorer von Visual Studio im Ordner **Roles** des Azure-Bereitstellungsprojekts mit der rechten Maustaste auf Ihre Webrolle oder Workerrolle, und klicken Sie dann auf **Properties**.

    ![Blob5](./media/storage-dotnet-how-to-use-queues-20/blob5.png)

2.  Klicken Sie auf die Registerkarte **Settings**, und wählen Sie die Schaltfläche **Add Setting** aus.

    ![Blob6](./media/storage-dotnet-how-to-use-queues-20/blob6.png)

    In der Einstellungstabelle wird daraufhin der neue Eintrag **Setting1** angezeigt.

3.  Wählen Sie in der Dropdownliste **Type** des neuen Eintrags **Setting1** die Option **Connection String** aus.

    ![Blob7](./media/storage-dotnet-how-to-use-queues-20/blob7.png)

4.  Klicken Sie auf die Schaltfläche **...** rechts neben dem Eintrag **Setting1**. Das Dialogfeld **Storage Account Connection String** wird geöffnet.

5.  Wählen Sie aus, ob das Ziel der Speicheremulator (simulierter Azure-Speicher auf dem lokalen Computer) oder ein Speicherkonto in der Cloud sein soll. Der Code in diesem Leitfaden kann mit beiden Optionen verwendet werden. Geben Sie den Wert für **Primary Access Key** ein, den Sie an früherer Stelle in diesem Lernprogramm kopiert haben, wenn Sie Warteschlangendaten in dem Speicherkonto speichern möchten, das zuvor in Azure erstellt wurde.

    ![Blob8](./media/storage-dotnet-how-to-use-queues-20/blob8.png)

6.  Ändern Sie den Eintrag **Name** von **Setting1** in einen benutzerfreundlicheren Namen wie **StorageConnectionString**. Sie verweisen später im Code in diesem Leitfaden auf diese Verbindungszeichenfolge.

    ![Blob9](./media/storage-dotnet-how-to-use-queues-20/blob9.png)

### Konfigurieren der Verbindungszeichenfolge in der .NET-Konfiguration

Wenn Sie eine Anwendung schreiben, bei der es sich nicht um einen Azure-Clouddienst handelt (siehe vorheriger Abschnitt), empfiehlt es sich, das .NET-Konfigurationssystem (z. B. `web.config` oder `app.config`) zu verwenden. Dies umfasst Azure-Websites oder virtuelle Azure-Maschinen sowie Anwendungen, die für die Ausführung außerhalb von Azure konzipiert sind. Verwenden Sie folgenden Code, um die Verbindungszeichenfolge mit dem Element `<appSettings>` zu speichern:

    <configuration>
		<appSettings>
        	<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey" />
		</appSettings\>
    </configuration>

Weitere Informationen zu Speicherverbindungszeichenfolgen finden Sie unter [Konfigurieren von Windows Azure-Verbindungszeichenfolgen](http://msdn.microsoft.com/en-us/library/windowsazure/ee758697.aspx).

Sie sind nun bereit für die Gewusst-wie-Aufgaben in diesem Leitfaden.

Programmgesteuerter ZugriffGewusst wie: Programmgesteuertes Zugreifen auf Warteschlangen mithilfe von .NET
----------------------------------------------------------------------------------------------------------

### Abrufen der Assembly

Mithilfe von NuGet können Sie die `Microsoft.WindowsAzure.Storage.dll`-Assembly abrufen. Klicken Sie im ****Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie **Manage NuGet Packages** aus. Suchen Sie online nach "WindowsAzure.Storage", und klicken Sie auf **Install**, um das Azure Storage-Paket und die zugehörigen Abhängigkeiten zu installieren.

`Microsoft.WindowsAzure.Storage.dll` ist auch im Azure SDK für .NET enthalten, das aus dem [.NET Developer Center](http://www.windowsazure.com/en-us/develop/net/#) heruntergeladen werden kann. Die Assembly wird im Verzeichnis `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\` installiert.

### Namespace-Deklarationen

Fügen Sie zu Beginn aller C\#-Dateien, in denen Sie programmgesteuert auf Azure Storage zugreifen möchten, die folgenden Namespace-Codedeklarationen hinzu:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;

Stellen Sie sicher, dass auf die `Microsoft.WindowsAzure.Storage.dll`-Assembly verwiesen wird.

### Abrufen der Verbindungszeichenfolge

Sie können Ihre Speicherkontoinformationen mit dem Typ **CloudStorageAccount** darstellen. Wenn Sie eine Windows Azure-Projektvorlage verwenden und/oder über einen Verweis auf "Microsoft.WindowsAzure.CloudConfigurationManager" verfügen, können Sie Ihre Speicherverbindungszeichenfolge und Speicherkontoinformationen mit dem Typ **CloudConfigurationManager** aus der Azure-Dienstkonfiguration abrufen:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Wenn Sie eine Anwendung ohne einen Verweis auf "Microsoft.WindowsAzure.CloudConfigurationManager" erstellen und die Verbindungszeichenfolge sich in `web.config` oder `app.config` befindet, wie oben gezeigt, können Sie die Verbindungszeichenfolge mit **ConfigurationManager** abrufen. Sie müssen einen Verweis auf "System.Configuration.dll" zu Ihrem Projekt und eine andere Namespace-Deklaration dafür hinzufügen:

    using System.Configuration;
    ...
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

### ODataLib-Abhängigkeiten
ODataLib-Abhängigkeiten in der Speicherclientbibliothek für .NET werden durch die ODataLib-Pakete (Version 5.0.2), die über NuGet verfügbar sind, und nicht durch WCF Data Services aufgelöst. Die ODataLib-Bibliotheken können direkt heruntergeladen werden, oder es wird über Ihr Codeprojekt durch NuGet darauf verwiesen. Die spezifischen ODataLib-Pakete sind [OData](http://nuget.org/packages/Microsoft.Data.OData/5.0.2), [Edm](http://nuget.org/packages/Microsoft.Data.Edm/5.0.2) und [Spatial](http://nuget.org/packages/System.Spatial/5.0.2).

Erstellen einer WarteschlangeGewusst wie: Erstellen einer Warteschlange
-----------------------------------------------------------------------

Mit einem **CloudQueueClient**-Objekt können Sie Referenzobjekte für Warteschlangen abrufen. Mit dem folgenden Code wird ein **CloudQueueClient**-Objekt erstellt. Der gesamte Code in diesem Leitfaden verwendet eine in der Dienstkonfiguration der Azure-Anwendung gespeicherte Speicherverbindungszeichenfolge. Es gibt auch andere Möglichkeiten zum Erstellen eines **CloudStorageAccount**-Objekts. Ausführliche Informationen dazu finden Sie in der [CloudStorageAccount](http://msdn.microsoft.com/en-us/library/microsoft.windowsazure.cloudstorageaccount_methods.aspx)-Dokumentation.

    // Speicherkonto aus Verbindungszeichenfolge abrufen.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Warteschlangenclient erstellen.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

Mithilfe des **queueClient**-Objekts können Sie einen Verweis auf die Warteschlange abrufen, die Sie verwenden möchten. Sie können die Warteschlange erstellen, wenn sie nicht vorhanden ist:

    // Verweis auf eine Warteschlange abrufen.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Warteschlange erstellen, wenn sie noch nicht vorhanden ist.
    queue.CreateIfNotExists();

Einfügen einer NachrichtGewusst wie: Einfügen einer Nachricht in eine Warteschlange
-----------------------------------------------------------------------------------

Um eine Nachricht in eine vorhandene Warteschlange einzufügen, erstellen Sie zuerst ein neues **CloudQueueMessage**-Objekt. Anschließend rufen Sie die **AddMessage**-Methode auf. Die **CloudQueueMessage** kann entweder aus einer Zeichenfolge (im Format UTF-8) oder aus einem **Byte**-Array erstellt werden. Dieser Code erstellte eine Warteschlange (wenn sie noch nicht vorhanden ist) und fügt die Nachricht "Hello, World" ein:

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

Einsehen der nächsten NachrichtGewusst wie: Einsehen der nächsten Nachricht
---------------------------------------------------------------------------

Sie können einen Blick auf die Nachricht am Anfang einer Warteschlange werfen, ohne sie aus der Warteschlange zu entfernen, indem Sie die Methode **PeekMessage** aufrufen.

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

Ändern des Inhalts einer NachrichtGewusst wie: Ändern des Inhalts von Nachrichten in der Warteschlange
------------------------------------------------------------------------------------------------------

Sie können den Inhalt einer Nachricht vor Ort in der Warteschlange ändern. Wenn die Nachricht eine Arbeitsaufgabe darstellt, können Sie diese Funktion verwenden, um den Status der Aufgabe zu aktualisieren. Mit dem folgenden Code wird die Warteschlangennachricht mit neuem Inhalt aktualisiert und das Sichtbarkeits-Zeitlimit um weitere 60 Sekunden verlängert. Dadurch wird der mit der Nachricht verknüpfte Arbeitsstatus gespeichert, und der Client erhält eine weitere Minute zur Bearbeitung der Nachricht. Sie können diese Technik verwenden, um Workflows mit mehreren Schritten in Warteschlangennachrichten zu verfolgen, ohne von vorn beginnen zu müssen, wenn ein Verarbeitungsschritt aufgrund eines Hardware- oder Softwarefehlers fehlschlägt. In der Regel behalten Sie auch die Anzahl der Wiederholungen bei, und wenn die Nachricht mehr als *n* Mal wiederholt wurde, wird sie gelöscht. Dies verhindert, dass eine Nachricht bei jeder Verarbeitung einen Anwendungsfehler auslöst.

    // Speicherkonto aus Verbindungszeichenfolge abrufen.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Warteschlangenclient erstellen.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Verweis auf eine Warteschlange abrufen.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Nachricht aus der Warteschlange abrufen und Inhalt ändern.
    CloudQueueMessage message = queue.GetMessage();
    message.SetMessageContent("Aktualisierter Inhalt.") ;
    queue.UpdateMessage(message, 
        TimeSpan.FromSeconds(0.0),  // Sofort anzeigen.
        MessageUpdateFields.Content | MessageUpdateFields.Visibility);

Entfernen der nächsten Nachricht aus der WarteschlangeGewusst wie: Entfernen der nächsten Nachricht aus der Warteschlange
-------------------------------------------------------------------------------------------------------------------------

Dieser Code entfernt eine Nachricht in zwei Schritten aus der Warteschlange. Wenn Sie **GetMessage** aufrufen, wird die nächste Nachricht aus der Warteschlange abgerufen. Die für **GetMessage** zurückgegebene Nachricht ist für andere Codes nicht mehr sichtbar, die Nachrichten aus dieser Warteschlange lesen. Standardmäßig bleibt die Nachricht 30 Sekunden lang unsichtbar. Um die Nachricht endgültig aus der Warteschlange zu entfernen, müssen Sie außerdem **DeleteMessage** aufrufen. Dieser zweistufige Prozess zum Entfernen von Nachrichten stellt sicher, dass eine andere Codeinstanz dieselbe Nachricht erneut abrufen kann, falls die Verarbeitung aufgrund eines Hardware- oder Softwarefehlers fehlschlägt. Der Code ruft **DeleteMessage** direkt nach der Verarbeitung der Nachricht auf.

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

Weitere Optionen für das Entfernen aus der WarteschlangeGewusst wie: Nutzen zusätzlicher Optionen für das Entfernen von Nachrichten aus der Warteschlange
---------------------------------------------------------------------------------------------------------------------------------------------------------

Es gibt zwei Möglichkeiten, wie Sie das Abrufen von Nachrichten aus der Warteschlange anpassen können. Erstens können Sie einen Nachrichtenstapel abrufen (bis zu 32). Zweitens können Sie das Unsichtbarkeits-Zeitlimit verkürzen oder verlängern, sodass der Code mehr oder weniger Zeit zur vollständigen Verarbeitung jeder Nachricht benötigt. Im folgenden Codebeispiel wird **GetMessages** verwendet, um 20 Nachrichten mit einem Aufruf abzurufen. Anschließend wird jede Nachricht mithilfe einer **foreach**-Schleife verarbeitet. Außerdem wird das Unsichtbarkeits-Zeitlimit auf fünf Minuten pro Nachricht festgelegt. Beachten Sie, dass die 5 Minuten für alle Nachrichten gleichzeitig beginnen, sodass 5 Minuten nach dem Aufruf von **GetMessages** alle Nachrichten, die nicht gelöscht wurden, wieder sichtbar werden.

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

Abrufen der WarteschlangenlängeGewusst wie: Abrufen der Warteschlangenlänge
---------------------------------------------------------------------------

Sie können die Anzahl der Nachrichten in einer Warteschlange schätzen lassen. Die Methode **FetchAttributes** fordert den Warteschlangendienst auf, die Warteschlangenattribute einschließlich der Nachrichtenanzahl abzurufen. Die Eigenschaft **ApproximateMethodCount** gibt den letzten von der Methode **FetchAttributes** abgerufenen Wert zurück, ohne den Warteschlangendienst aufzurufen.

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
    int
         cachedMessageCount = queue.ApproximateMessageCount;

    // Nachrichtenanzahl anzeigen.
    Console.WriteLine("Nachrichtenanzahl in Warteschlange: " + cachedMessageCount);

Löschen einer WarteschlangeGewusst wie: Löschen einer Warteschlange
-------------------------------------------------------------------

Zum Löschen einer Warteschlange und aller darin enthaltenen Nachrichten rufen Sie die Methode **Delete** für das Warteschlangenobjekt auf.

    // Speicherkonto aus Verbindungszeichenfolge abrufen.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Warteschlangenclient erstellen.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Verweis auf eine Warteschlange abrufen.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Warteschlange löschen.
    queue.Delete();

Nächste Schritte
----------------

Nachdem Sie sich nun mit den Grundlagen des Warteschlangenspeichers vertraut gemacht haben, folgen Sie diesen Links, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.

-   Vollständige Informationen zu verfügbaren APIs finden Sie in der Warteschlangendienst-Referenzdokumentation:
    -   [Referenz zur Speicherclientbibliothek für .NET](http://msdn.microsoft.com/en-us/library/windowsazure/dn495001(v=azure.10).aspx)
    -   [REST-API-Referenz](http://msdn.microsoft.com/en-us/library/windowsazure/dd179355)
-   Weitere Informationen zu fortgeschrittenen Aufgaben mit Azure Storage finden Sie unter [Speichern und Zugreifen auf Daten in Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx).
-   Weitere Informationen zu zusätzlichen Optionen für das Speichern von Daten in Azure finden Sie in den anderen Featureleitfäden.
    -   Verwenden Sie [Tabellenspeicher](/en-us/develop/net/how-to-guides/table-services/) zum Speichern strukturierter Daten.
    -   Verwenden Sie [Blobspeicher](/en-us/develop/net/how-to-guides/blob-storage/) zum Speichern unstrukturierter Daten.
    -   Verwenden Sie eine [SQL-Datenbank](/en-us/develop/net/how-to-guides/sql-database/) zum Speichern relationaler Daten.


[Next Steps]: #next-steps
  [What is Queue Storage]: #what-is
  [Concepts]: #concepts
  [Create an Azure Storage Account]: #create-account
  [Setup an Azure Storage Connection String]: #setup-connection-string
  [How to: Programmatically access queues using .NET]: #access
  [How To: Create a Queue]: #create-queue
  [How To: Insert a Message into a Queue]: #insert-message
  [How To: Peek at the Next Message]: #peek-message
  [How To: Change the Contents of a Queued Message]: #change-contents
  [How To: Dequeue the Next Message]: #get-message
  [How To: Leverage Additional Options for Dequeuing Messages]: #advanced-get
  [How To: Get the Queue Length]: #get-queue-length
  [How To: Delete a Queue]: #delete-queue
  [Download and install the Azure SDK for .NET]: /en-us/develop/net/
  [.NET client library reference]: http://msdn.microsoft.com/en-us/library/windowsazure/dn495001(v=azure.10).aspx
  [Creating a Azure Project in Visual Studio]: http://msdn.microsoft.com/en-us/library/windowsazure/ee405487.aspx 
  [CloudStorageAccount]: http://msdn.microsoft.com/en-us/library/microsoft.windowsazure.cloudstorageaccount_methods.aspx
  [Storing and Accessing Data in Azure]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx
  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configuring Connection Strings]: http://msdn.microsoft.com/en-us/library/windowsazure/ee758697.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2