<properties linkid="dev-net-how-to-use-queue-storage-service-java" urlDisplayName="Queue Service" pageTitle="How to use the queue service (Java) | Microsoft Azure" metaKeywords="Azure Queue Service, Azure Queue storage service, queues peeking, queues insert messages, queues get messages, queues delete messages, create queues, delete queues, Queue service Java" description="Learn how to use the Azure Queue service to create and delete queues, and insert, get, and delete messages. Samples written in Java." metaCanonical="" services="storage" documentationCenter="Java" title="How to use the Queue storage service from Java" authors="" solutions="" manager="" editor="" />

Verwenden des Tabellenspeicherdiensts in Java
=============================================

In diesem Leitfaden wird die Durchführung häufiger Szenarien mit dem Azure-Warteschlangen-Speicherdienst demonstriert. Die Beispiele wurden in Java geschrieben und verwenden das [Azure-SDK für Java](http://www.windowsazure.com/en-us/develop/java/). Zu den Szenarien gehören das Einfügen, Einsehen, Abrufen und Löschen von Warteschlangennachrichten sowie das Erstellen und Löschen von Warteschlangen. Weitere Informationen zu Warteschlangen finden Sie im Abschnitt [Nächste Schritte](#NextSteps).

Inhaltsverzeichnis
------------------

-   [Was ist Warteschlangenspeicherung?](#what-is)
-   [Konzepte](#Concepts)
-   [Erstellen eines Azure-Speicherkontos](#CreateAccount)
-   [Erstellen einer Java-Anwendung](#CreateApplication)
-   [Konfigurieren Ihrer Anwendung für den Zugriff auf den Warteschlangenspeicher](#ConfigureStorage)
-   [Einrichten einer Azure-Speicherverbindungszeichenfolge](#ConnectionString)
-   [Gewusst wie: Erstellen einer Warteschlange](#create-queue)
-   [Gewusst wie: Hinzufügen von Nachrichten zu einer Warteschlange](#add-message)
-   [Gewusst wie: Einsehen der nächsten Nachricht](#peek-message)
-   [Gewusst wie: Entfernen der nächsten Nachricht](#dequeue-message)
-   [Gewusst wie: Ändern des Inhalts von Nachrichten in der Warteschlange](#change-message)
-   [Zusätzliche Optionen für das Entfernen von Nachrichten aus der Warteschlange](#additional-options)
-   [Gewusst wie: Abrufen der Warteschlangenlänge](#get-queue-length)
-   [Gewusst wie: Löschen einer Warteschlange](#delete-queue)
-   [Nächste Schritte](#NextSteps)

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

Erstellen eines Azure-Speicherkontos
------------------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Erstellen einer Java-Anwendung
------------------------------

In diesem Leitfaden werden Sie Speicherfunktionen verwenden, die lokal in einer Java-Anwendung oder in Code ausgeführt werden können, der in einer Webrolle oder in einer Workerrolle in Azure ausgeführt wird. Es wird vorausgesetzt, dass Sie das Java Development Kit (JDK) heruntergeladen und installiert und die Anweisungen auf der [Downloadseite des Azure-SDK für Java](http://www.windowsazure.com/en-us/develop/java/) ausgeführt haben, um die Azure-Bibliotheken für Java und das Azure-SDK zu installieren, und dass Sie ein Azure-Speicherkonto in Ihrem Azure-Abonnement erstellt haben. Sie können beliebige Entwicklungstools zum Erstellen Ihrer Anwendung verwenden, einschließlich Editor. Sie müssen lediglich wissen, wie Sie ein Java-Projekt kompilieren und auf die Azure-Bibliotheken für Java verweisen.

Konfigurieren Ihrer Anwendung für den Zugriff auf den Warteschlangenspeicher
----------------------------------------------------------------------------

Fügen Sie folgende Import-Anweisungen am Anfang der Java-Datei dort ein, wo Azure-Speicher-APIs auf Warteschlangen zugreifen sollen:

    // Folgende Importanweisungen für die Verwendung von Warteschlangen-APIs einschließen
    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.queue.client.*;

Einrichten einer Azure-Speicherverbindungszeichenfolge
------------------------------------------------------

Ein Azure-Speicherclient verwendet eine Speicherverbindungszeichenfolge zum Speichern von Endpunkten und Anmeldeinformationen für den Zugriff auf Datenverwaltungsdienste. Bei der Ausführung in einer Clientanwendung muss die Speicherverbindungszeichenfolge in dem unten gezeigten Format angegeben werden. Dabei müssen der Name Ihres Speicherkontos und der primäre Zugriffsschlüssel für das im Verwaltungsportal aufgeführte Speicherkonto als *AccountName*- und *AccountKey*- Werte eingegeben werden. Dieses Beispiel zeigt, wie Sie ein statisches Feld für die Verbindungszeichenfolge deklarieren:

    // Define the connection-string with your values
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

In einer Anwendung, die in einer Azure-Rolle ausgeführt wird, kann diese Zeichenfolge in der Dienstkonfigurationsdatei ServiceConfiguration.cscfg gespeichert werden. Der Zugriff darauf kann dann durch Aufruf der Methode RoleEnvironment.getConfigurationSettings erfolgen. Dieses Beispiel zeigt, wie die Verbindungszeichenfolge von einem **Setting**-Element mit der Bezeichnung *StorageConnectionString* in der Dienstkonfigurationsdatei abgerufen wird:

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

Gewusst wie: Erstellen einer Warteschlange
------------------------------------------

Mit einem CloudQueueClient-Objekt können Sie Referenzobjekte für Warteschlangen abrufen. Der folgende Code erstellt ein CloudQueueClient-Objekt.

Der gesamte Code in diesem Leitfaden verwendet eine auf eine der beiden oben beschriebenen Arten deklarierte Speicherverbindungszeichenfolge. Es gibt auch andere Möglichkeiten, CloudStorageAccount-Objekte zu erstellen. Details dazu finden Sie in der Javadocs-Dokumentation für CloudStorageAccount.

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Warteschlangen-Client erstellen
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

Mit dem CloudQueueClient-Objekt können Sie einen Verweis auf die Warteschlange abrufen, die Sie verwenden möchten. Sie können die Warteschlange erstellen, wenn sie nicht vorhanden ist.

    // Verweis auf die Warteschlange abrufen
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Warteschlange erstellen, falls diese nicht existiert
    queue.createIfNotExist();

Gewusst wie: Hinzufügen von Nachrichten zu einer Warteschlange
--------------------------------------------------------------

Um eine Nachricht in eine vorhandene Warteschlange einzufügen, erstellen Sie zuerst ein neues CloudQueueMessage-Objekt. Anschließend rufen Sie die addMessage-Methode auf. Die CloudQueueMessage kann entweder aus einer Zeichenfolge (im UTF-8-Format) oder aus einem Byte-Array erstellt werden. Dieser Code erstellte eine Warteschlange (falls noch nicht vorhanden) und fügt die Nachricht "Hello, World" ein:

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Warteschlangen-Client erstellen
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Verweis auf die Warteschlange abrufen
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Warteschlange erstellen, falls diese nicht existiert
    queue.createIfNotExist();

    // Nachricht erstellen und zur Warteschlange hinzufügen
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.addMessage(message);

Gewusst wie: Einsehen der nächsten Nachricht
--------------------------------------------

Sie können einen Blick auf die Nachricht am Anfang einer Warteschlange werfen, ohne sie aus der Warteschlange zu entfernen, indem Sie die Methode peekMessage aufrufen.

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Warteschlangen-Client erstellen
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Verweis auf die Warteschlange abrufen
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Nächste Nachricht einsehen
    CloudQueueMessage peekedMessage = queue.peekMessage();

Gewusst wie: Entfernen der nächsten Nachricht
---------------------------------------------

Dieser Code entfernt eine Nachricht in zwei Schritten aus der Warteschlange. Wenn Sie retrieveMessage aufrufen, wird die nächste Nachricht aus der Warteschlange abgerufen. Die von retrieveMessage zurückgegebene Nachricht ist für anderen Code nicht mehr sichtbar, der Nachrichten aus dieser Warteschlange liest. Standardmäßig bleibt die Nachricht 30 Sekunden lang unsichtbar. Um die Nachricht endgültig aus der Warteschlange zu entfernen, müssen Sie außerdem deleteMessage aufrufen. Dieser zweistufige Prozess zum Entfernen von Nachrichten stellt sicher, dass eine andere Codeinstanz dieselbe Nachricht erneut abrufen kann, falls die Verarbeitung aufgrund eines Hardware- oder Softwarefehlers fehlschlägt. Der Code ruft deleteMessage direkt nach der Verarbeitung der Nachricht auf.

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Warteschlangen-Client erstellen
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Verweis auf die Warteschlange abrufen
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Erste sichtbare Nachricht aus der Warteschlange abrufen
    CloudQueueMessage retrievedMessage = queue.retrieveMessage();

    // Nachricht in unter 30 Sekunden verarbeiten und anschließend löschen.
    queue.deleteMessage(retrievedMessage);

Gewusst wie: Ändern des Inhalts von Nachrichten in der Warteschlange
--------------------------------------------------------------------

Sie können den Inhalt einer Nachricht vor Ort in der Warteschlange ändern. Wenn die Nachricht eine Arbeitsaufgabe darstellt, können Sie diese Funktion verwenden, um den Status der Aufgabe zu aktualisieren. Mit dem folgenden Code wird die Warteschlangennachricht mit neuem Inhalt aktualisiert und das Sichtbarkeits-Zeitlimit um weitere 60 Sekunden verlängert. Dadurch wird der mit der Nachricht verknüpfte Arbeitsstatus gespeichert, und der Client erhält eine weitere Minute zur Bearbeitung der Nachricht. Sie können diese Technik verwenden, um Workflows mit mehreren Schritten in Warteschlangennachrichten zu verfolgen, ohne von vorn beginnen zu müssen, wenn ein Verarbeitungsschritt aufgrund eines Hardware- oder Softwarefehlers fehlschlägt. In der Regel behalten Sie auch die Anzahl der Wiederholungen bei, und wenn die Nachricht mehr als n Mal wiederholt wurde, wird sie gelöscht. Dies verhindert, dass eine Nachricht bei jeder Verarbeitung einen Anwendungsfehler auslöst.

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Warteschlangen-Client erstellen
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Verweis auf die Warteschlange abrufen
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Erste sichtbare Nachricht aus der Warteschlange abrufen
    CloudQueueMessage message = queue.retrieveMessage();

    // Nachrichteninhalt ändern und Nachricht in 60 Sekunden wieder sichtbar machen
    message.setMessageContent("Updated contents.");
    EnumSet<MessageUpdateFields> updateFields = 
        EnumSet.of(MessageUpdateFields.CONTENT, MessageUpdateFields.VISIBILITY);
    queue.updateMessage(message, 60, updateFields, null, null);

Zusätzliche Optionen für das Entfernen von Nachrichten aus der Warteschlange
----------------------------------------------------------------------------

Es gibt zwei Möglichkeiten, wie Sie das Abrufen von Nachrichten aus der Warteschlange anpassen können. Erstens können Sie einen Nachrichtenstapel abrufen (bis zu 32). Zweitens können Sie das Unsichtbarkeits-Zeitlimit verkürzen oder verlängern, sodass der Code mehr oder weniger Zeit zur vollständigen Verarbeitung jeder Nachricht benötigt.

Im folgenden Codebeispiel wird retrieveMessages verwendet, um 20 Nachrichten mit einem Aufruf abzurufen. Anschließend wird jede Nachricht mithilfe einer **for**-Schleife verarbeitet. Außerdem wird das Unsichtbarkeits-Zeitlimit auf fünf Minuten (300 Sekunden) pro Nachricht festgelegt. Beachten Sie, dass die fünf Minuten für alle Nachrichten gleichzeitig beginnen, sodass fünf Minuten nach dem Aufruf von retrieveMessages alle Nachrichten, die nicht gelöscht wurden, wieder sichtbar werden.

    // Speicherkonto aus Verbindungszeichenfolge abrufen
        CloudStorageAccount storageAccount = 
            CloudStorageAccount.parse(storageConnectionString);
        
        // Warteschlangen-Client erstellen
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();
        
        // Verweis auf die Warteschlange abrufen
        CloudQueue queue = queueClient.getQueueReference("myqueue");
        
        // 20 Nachrichten mit einem Sichtbarkeits-Zeitlimit von 300 Sekunden aus der Warteschlange abrufen
        for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
            // Alle Nachrichten in unter 5 Minuten verarbeiten und die
            // Nachrichten nach der Verarbeitung löschen.
        queue.deleteMessage(message);
        }

Gewusst wie: Abrufen der Warteschlangenlänge
--------------------------------------------

Sie können die Anzahl der Nachrichten in einer Warteschlange schätzen lassen. Die downloadAttributes -Methode fragt verschiedene aktuelle Werte, darunter die Anzahl der Nachrichten in einer Warteschlange, aus dem Warteschlangendienst ab. Die Anzahl ist nur ein ungefährer Wert, da seit der Antwort des Warteschlangendienstes möglicherweise bereits Nachrichten hinzugefügt oder gelöscht wurden. Die getApproximateMethodCount-Methode gibt den letzten von downloadAttributes abgerufenen Wert zurück, ohne den Warteschlangendienst aufzurufen.

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Warteschlangen-Client erstellen
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Verweis auf die Warteschlange abrufen
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Geschätzte Nachrichtenanzahl vom Server herunterladen
    queue.downloadAttributes();

    // Neu zwischengespeicherte geschätzte Nachrichtenanzahl abrufen
    long cachedMessageCount = queue.getApproximateMessageCount();

Gewusst wie: Löschen einer Warteschlange
----------------------------------------

Zum Löschen einer Warteschlange und aller darin enthaltenen Nachrichten rufen Sie die Methode delete für das Warteschlangenobjekt auf.

    // Speicherkonto aus Verbindungszeichenfolge abrufen
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Warteschlangen-Client erstellen
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Verweis auf die Warteschlange abrufen
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Warteschlange löschen
    queue.delete();

Nächste Schritte
----------------

Nachdem Sie sich nun mit den Grundlagen des Warteschlangenspeichers vertraut gemacht haben, folgen Sie diesen Links, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Speichern und Zugreifen auf Daten in Azure]
-   Besuchen Sie den Blog des Azure-Speicherteams: &lt;http://blogs.msdn.com/b/windowsazurestorage/\&gt;

