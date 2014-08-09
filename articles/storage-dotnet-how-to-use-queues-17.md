<properties linkid="dev-net-2-how-to-queue-service" urlDisplayName="Queue Service (2.0)" pageTitle="How to use the queue storage service | Microsoft Azure" metaKeywords="Get started Azure queue, Azure asynchronous processing, Azure queue, Azure queue storage, Azure queue .NET, Azure queue storage .NET, Azure queue C#, Azure queue storage C#" description="Learn how to use the Azure queue storage service to create and delete queues and insert, peek, get, and delete queue messages." metaCanonical="" services="storage" documentationCenter=".NET" title="How to use the Queue Storage Service" authors="" solutions="" manager="paulettm" editor="cgronlun" />

Verwenden des Warteschlangenspeicher-Diensts
============================================

[Version 1.7](/de-de/develop/net/how-to-guides/queue-service-v17/ "Version 1.7") [Version 2.0](/de-de/develop/net/how-to-guides/queue-service/ "Version 2.0")

In diesem Leitfaden wird die Durchführung häufiger Szenarien mit dem Azure-Warteschlangen-Speicherdienst demonstriert. Die Beispiele sind in C\#-Code geschrieben und verwenden die .NET API. Zu den Szenarien gehören das **Einfügen**, **Einsehen**, **Abrufen** und **Löschen** von Warteschlangennachrichten sowie das **Erstellen und Löschen von Warteschlangen**. Weitere Informationen zu Warteschlangen finden Sie im Abschnitt [Nächste Schritte](#next-steps).

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
-   [Gewusst wie: Entfernen der nächsten Nachricht](#get-message)
-   [Gewusst wie: Zusätzliche Optionen für das Entfernen von Nachrichten aus der Warteschlange](#advanced-get)
-   [Gewusst wie: Abrufen der Warteschlangenlänge](#get-queue-length)
-   [Gewusst wie: Löschen einer Warteschlange](#delete-queue)
-   [Nächste Schritte](#next-steps)

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

Erstellen eines KontosErstellen eines Azure-Speicherkontos
----------------------------------------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Einrichten einer VerbindungszeichenfolgeEinrichten einer Azure-Speicherverbindungszeichenfolge
----------------------------------------------------------------------------------------------

Die Azure .NET Speicher-API unterstützt Speicher-Verbindungszeichenfolgen zum Konfigurieren von Endpunkten und Anmeldeinformationen für den Zugriff auf Speicherdienste. Sie können Ihre Speicher-Verbindungszeichenfolge in einer Konfigurationsdatei angeben, anstatt sie hart zu codieren:

-   Bei der Verwendung von Azure-Cloud-Diensten sollten Sie Ihre Verbindungszeichenfolge mithilfe des Azure-Dienstkonfigurationssystems speichern (`*.csdef`- und `*.cscfg`-Dateien).
-   Bei der Verwendung von Azure-Websites oder virtuellen Computern in Azure sollten Sie Ihre Verbindungszeichenfolge mithilfe des .NET-Konfigurationssystems speichern (`web.config`-Datei).

In beiden Fällen können Sie Ihre Verbindungszeichenfolge über die `CloudConfigurationManager.GetSetting`-Methode abrufen, wie später in dieser Anleitung beschrieben.

### Konfigurieren Ihrer Verbindungszeichenfolge für Cloud-Dienste

Azure-Cloud-Dienst-Projekte bieten einen einzigartigen Dienstkonfigurationsmechanismus, mit dem Sie Ihre Konfigurationseinstellungen im Azure-Verwaltungsportal dynamisch ändern können, ohne Ihre Anwendung neu bereitstellen zu müssen.

So konfigurieren Sie die Verbindungszeichenfolge in der Azure-Dienstkonfiguration:

1.  Klicken Sie im Projektmappen-Explorer von Visual Studio im Ordner **Roles** des Azure-Bereitstellungsprojekts mit der rechten Maustaste auf Ihre Webrolle oder Workerrolle, und klicken Sie dann auf **Properties**.  
     ![Blob5](./media/storage-dotnet-how-to-use-queues-17/blob5.png)

2.  Klicken Sie auf die Registerkarte **Settings** und anschließend auf die Schaltfläche **Add Setting** hinzufügen.  
     ![Blob6](./media/storage-dotnet-how-to-use-queues-17/blob6.png)

    In der Einstellungstabelle wird daraufhin der neue Eintrag **Setting1** angezeigt.

3.  Wählen Sie in der Dropdownliste **Type** des neuen Eintrags **Setting1** die Option **Connection String** aus.  
     ![Blob7](./media/storage-dotnet-how-to-use-queues-17/blob7.png)

4.  Klicken Sie auf die Schaltfläche **...** rechts neben dem Eintrag **Setting1**. Daraufhin öffnet sich der Dialogfeld **Storage Account Connection String**.

5.  Wählen Sie aus, ob das Ziel der Speicheremulator (simulierter Azure-Speicher auf dem lokalen Computer) oder ein Speicherkonto in der Cloud sein soll. Der Code in diesem Leitfaden kann mit beiden Optionen verwendet werden. Geben Sie den Wert für **Primary Access Key** ein, den Sie an früherer Stelle in diesem Lernprogramm kopiert haben, wenn Sie Blob-Daten in dem zuvor in Azure erstellten Speicherkonto speichern möchten.

    ![Blob8](./media/storage-dotnet-how-to-use-queues-17/blob8.png)

6.  Ändern Sie **Name** von **Setting1** zu einem "lesbareren" Namen wie z. B. **StorageConnectionString**. Sie werden später in diesem Leitfaden auf diese Verbindungszeichenfolge verweisen.

    ![Blob9](./media/storage-dotnet-how-to-use-queues-17/blob9.png)

### Konfigurieren der Verbindungszeichenfolge bei Verwendung von Websites oder virtuellen Computern

Bei der Verwendung von Websites oder virtuellen Computern sollten Sie das .NET-Konfigurationssystem verwenden (`web.config`). Sie speichern die Verbindungszeichenfolge mit dem Element `<appSettings>`:

    <configuration>
        <appSettings>
            <add key="StorageConnectionString"
                 value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
        </appSettings>
    </configuration>

Weitere Informationen zu Speicherverbindungszeichenfolgen finden Sie unter [Konfigurieren von Azure-Verbindungszeichenfolgen](http://msdn.microsoft.com/de-de/library/windowsazure/ee758697.aspx).

Sie sind nun bereit, um die einzelnen Aufgaben in dieser Anleitung auszuführen.

Programmgesteuerter ZugriffGewusst wie: Programmgesteuertes Zugreifen auf Warteschlangen mithilfe von .NET
----------------------------------------------------------------------------------------------------------

Fügen Sie die folgenden Namespace-Deklarationen zum Anfang aller C\#-Dateien hinzu, in denen Sie programmgesteuert auf den Azure-Speicher zugreifen möchten:

    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.StorageClient;

Sie können Ihre Speicherverbindungszeichenfolge und Speicherkontoinformationen mit dem Typ **CloudStorageAccount** und dem Typ **CloudConfigurationManager** aus der Azure-Dienstkonfiguration abrufen:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Erstellen einer WarteschlangeGewusst wie: Erstellen einer Warteschlange
-----------------------------------------------------------------------

Mit einem **CloudQueueClient**-Objekt können Sie Referenzobjekte für Warteschlangen abrufen. Der folgende Code erstellt ein **CloudQueueClient**-Objekt. Der gesamte Code in diesem Leitfaden verwendet eine in der Dienstkonfiguration der Azure-Anwendung gespeicherte Speicherverbindungszeichenfolge. Es gibt noch weitere Möglichkeiten zum Erstellen eines **CloudStorageAccount**-Objekts. Ausführliche Informationen dazu finden Sie in der [CloudStorageAccount](http://msdn.microsoft.com/de-de/library/microsoft.windowsazure.cloudstorageaccount_methods.aspx)-Dokumentation.

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Warteschlangen-Client erstellen
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

Mithilfe des **queueClient**-Objekts können Sie einen Verweis auf die Warteschlange abrufen, die Sie verwenden möchten. Sie können die Warteschlange erstellen, wenn sie nicht vorhanden ist.

    // Verweis auf die Warteschlange abrufen
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Warteschlange erstellen, falls diese nicht existiert
    queue.CreateIfNotExist();

Einfügen einer NachrichtGewusst wie: Einfügen einer Nachricht in eine Warteschlange
-----------------------------------------------------------------------------------

Um eine Nachricht in eine vorhandene Warteschlange einzufügen, erstellen Sie zuerst ein neues **CloudQueueMessage**-Objekt. Anschließend rufen Sie die **AddMessage**-Methode auf. Die **CloudQueueMessage** kann entweder aus einer Zeichenfolge (im UTF-8-Format) oder aus einem **Byte**-Array erstellt werden. Dieser Code erstellte eine Warteschlange (falls noch nicht vorhanden) und fügt die Nachricht "Hello, World" ein:

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Warteschlangen-Client erstellen
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Verweis auf die Warteschlange abrufe
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Warteschlange erstellen, falls diese nicht existiert
    queue.CreateIfNotExist();

    // Nachricht erstellen und zur Warteschlange hinzufügen
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.AddMessage(message);

Einsehen der nächsten NachrichtGewusst wie: Einsehen der nächsten Nachricht
---------------------------------------------------------------------------

Sie können einen Blick auf die Nachricht am Anfang einer Warteschlange werfen, ohne sie aus der Warteschlange zu entfernen, indem Sie die Methode **PeekMessages** aufrufen.

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Warteschlangen-Client erstellen
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Verweis auf die Warteschlange abrufem
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Nächste Nachricht einsehen
    CloudQueueMessage peekedMessage = queue.PeekMessage();

Ändern des Inhalts einer NachrichtGewusst wie: Ändern des Inhalts von Nachrichten in der Warteschlange
------------------------------------------------------------------------------------------------------

Sie können den Inhalt einer Nachricht vor Ort in der Warteschlange ändern. Wenn die Nachricht eine Arbeitsaufgabe darstellt, können Sie diese Funktion verwenden, um den Status der Aufgabe zu aktualisieren. Mit dem folgenden Code wird die Warteschlangennachricht mit neuem Inhalt aktualisiert und das Sichtbarkeits-Zeitlimit um weitere 60 Sekunden verlängert. Dadurch wird der mit der Nachricht verknüpfte Arbeitsstatus gespeichert, und der Client erhält eine weitere Minute zur Bearbeitung der Nachricht. Sie können diese Technik verwenden, um Workflows mit mehreren Schritten in Warteschlangennachrichten zu verfolgen, ohne von vorn beginnen zu müssen, wenn ein Verarbeitungsschritt aufgrund eines Hardware- oder Softwarefehlers fehlschlägt. In der Regel behalten Sie auch die Anzahl der Wiederholungen bei, und wenn die Nachricht mehr als *n* Mal wiederholt wurde, wird sie gelöscht. Dies verhindert, dass eine Nachricht bei jeder Verarbeitung einen Anwendungsfehler auslöst.

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Warteschlangen-Client erstellen
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Verweis auf die Warteschlange abrufem
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    CloudQueueMessage message = queue.GetMessage();
    message.SetMessageContent("Updated contents.") ;
    queue.UpdateMessage(message, 
        TimeSpan.FromSeconds(0.0),  // sofort sichtbar
        MessageUpdateFields.Content | MessageUpdateFields.Visibility);

Entfernen der nächsten NachrichtGewusst wie: Entfernen der nächsten Nachricht
-----------------------------------------------------------------------------

Dieser Code entfernt eine Nachricht in zwei Schritten aus der Warteschlange. Wenn Sie **GetMessage** aufrufen, wird die nächste Nachricht aus der Warteschlange abgerufen. Die für **GetMessages** zurückgegebene Nachricht ist für anderen Code nicht mehr sichtbar, der Nachrichten aus dieser Warteschlange liest. Standardmäßig bleibt die Nachricht 30 Sekunden lang unsichtbar. Um die Nachricht endgültig aus der Warteschlange zu entfernen, müssen Sie außerdem **DeleteMessage** aufrufen. Dieser zweistufige Prozess zum Entfernen von Nachrichten stellt sicher, dass eine andere Codeinstanz dieselbe Nachricht erneut abrufen kann, falls die Verarbeitung aufgrund eines Hardware- oder Softwarefehlers fehlschlägt. Der Code ruft **DeleteMessage** direkt nach der Verarbeitung der Nachricht auf.

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Warteschlangen-Client erstellen
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Verweis auf die Warteschlange abrufem
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Nächste Nachricht abrufen
    CloudQueueMessage retrievedMessage = queue.GetMessage();

    //Nachricht in weniger als 30 Sekunden verarbeiten und anschließend löschen
    queue.DeleteMessage(retrievedMessage);

Zusätzliche Optionen für das Entfernen aus der WarteschlangeGewusst wie: Zusätzliche Optionen für das Entfernen von Nachrichten aus der Warteschlange
-----------------------------------------------------------------------------------------------------------------------------------------------------

Es gibt zwei Möglichkeiten, wie Sie das Abrufen von Nachrichten aus der Warteschlange anpassen können. Erstens können Sie einen Nachrichtenstapel abrufen (bis zu 32). Zweitens können Sie das Unsichtbarkeits-Zeitlimit verkürzen oder verlängern, sodass der Code mehr oder weniger Zeit zur vollständigen Verarbeitung jeder Nachricht benötigt. Im folgenden Codebeispiel wird **GetMessages** verwendet, um 20 Nachrichten mit einem Aufruf abzurufen. Anschließend wird jede Nachricht mithilfe einer **foreach**-Schleife verarbeitet. Außerdem wird das Unsichtbarkeits-Zeitlimit auf fünf Minuten pro Nachricht festgelegt. Beachten Sie, dass die 5 Minuten für alle Nachrichten gleichzeitig beginnen, sodass 5 Minuten nach dem Aufruf von **GetMessages** alle Nachrichten, die nicht gelöscht wurden, wieder sichtbar werden.

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Warteschlangen-Client erstellen
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Verweis auf die Warteschlange abrufem
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Verarbeitung aller Nachrichten in unter 5 Minuten, Nachrichten werden nach Verarbeitung gelöscht
        queue.DeleteMessage(message);
    }

Abrufen der WarteschlangenlängeGewusst wie: Abrufen der Warteschlangenlänge
---------------------------------------------------------------------------

Sie können die Anzahl der Nachrichten in einer Warteschlange schätzen lassen. Die **RetrieveApproximateMessageCount**-Methode fragt die Anzahl der Nachrichten in einer Warteschlange aus dem Warteschlangendienst ab. Die Anzahl ist nur ein ungefährer Wert, da seit der Antwort des Warteschlangendienstes möglicherweise bereits Nachrichten hinzugefügt oder gelöscht wurden. Die Eigenschaft **ApproximateMethodCount** gibt den letzten von der Methode **RetrieveApproximateMessageCount** abgerufenen Wert zurück, ohne den Warteschlangendienst aufzurufen.

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Warteschlangen-Client erstellen
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Verweis auf die Warteschlange abrufem
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Geschätzte Nachrichtenanzahl abrufen
    int freshMessageCount = queue.RetrieveApproximateMessageCount();

    // Zwischengespeicherte geschätzte Nachrichtenanzahl abrufen
    int? cachedMessageCount = queue.ApproximateMessageCount;

Löschen einer WarteschlangeGewusst wie: Löschen einer Warteschlange
-------------------------------------------------------------------

Zum Löschen einer Warteschlange und aller darin enthaltenen Nachrichten rufen Sie die Methode **Delete** für das Warteschlangenobjekt auf.

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Warteschlangen-Client erstellen
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Verweis auf die Warteschlange abrufem
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Warteschlange löschen
    queue.Delete();

Nächste Schritte
----------------

Nachdem Sie sich nun mit den Grundlagen des Warteschlangenspeichers vertraut gemacht haben, folgen Sie diesen Links, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.

-   Vollständige Informationen zu verfügbaren APIs finden Sie in der Warteschlangendienst-Referenzdokumentation:
    -   [Referenz zur .NET-Clientbibliothek](http://msdn.microsoft.com/de-de/library/windowsazure/wl_svchosting_mref_reference_home)
    -   [REST-API-Referenz](http://msdn.microsoft.com/de-de/library/windowsazure/dd179355)
-   Weitere Informationen zu fortgeschrittenen Aufgaben mit Azure-Speicher finden Sie unter [Speichern und Zugreifen auf Daten in Azure](http://msdn.microsoft.com/de-de/library/windowsazure/gg433040.aspx).
-   Weitere Informationen zu zusätzlichen Optionen für das Speichern von Daten in Azure finden Sie in den anderen Featureleitfäden.
    -   Verwenden Sie [Tabellenspeicher](/de-de/develop/net/how-to-guides/table-services/) zum Speichern strukturierter Daten.
    -   Verwenden Sie [Blobspeicher](/de-de/develop/net/how-to-guides/blob-storage/) zum Speichern unstrukturierter Daten.
    -   Verwenden Sie eine [SQL-Datenbank](/de-de/develop/net/how-to-guides/sql-database/) zum Speichern relationaler Daten.

