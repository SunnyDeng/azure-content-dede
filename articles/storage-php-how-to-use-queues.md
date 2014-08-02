<properties title="How to use the queue service (PHP) - Azure feature guide" pageTitle="How to use the queue service (PHP) | Microsoft Azure" metaKeywords="Azure Queue Service messaging PHP" description="Learn how to use the Azure Queue service to create and delete queues, and insert, get, and delete messages. Samples written in PHP." documentationCenter="PHP" services="storage" authors="" />

Verwenden des Warteschlangendienstes aus PHP
============================================

Dieses Handbuch demonstriert Ihnen allgemeine Szenarien unter Verwendung des Warteschlangendienstes in Azure. Die Beispiele wurden unter Verwendung von Klassen des Windows SDK für PHP geschrieben. Zu den Szenarien gehören das **Einfügen**, **Einsehen**, **Abrufen** und **Löschen** von Warteschlangennachrichten sowie das **Erstellen und Löschen von Warteschlangen**. Weitere Informationen zu Warteschlangen finden Sie im Abschnitt [Nächste Schritte](#NextSteps).

Inhaltsverzeichnis
------------------

-   [Was ist Warteschlangenspeicherung](#what-is)
-   [Konzepte](#concepts)
-   [Erstellen eines Azure-Speicherkontos](#create-account)
-   [Erstellen einer PHP-Anwendung](#create-app)
-   [Konfigurieren Ihrer Anwendung für den Warteschlangendienst](#configure-app)
-   [Einrichten einer Azure-Speicherverbindung](#connection-string)
-   [Vorgehensweise: Erstellen einer Warteschlange](#create-queue)
-   [Vorgehensweise: Hinzufügen von Nachrichten zu einer Warteschlange](#add-message)
-   [Vorgehensweise: Einsehen der nächsten Nachricht](#peek-message)
-   [Vorgehensweise: Entfernen der nächsten Nachricht aus der Warteschlange](#dequeue-message)
-   [Vorgehensweise: Ändern des Inhalts von Nachrichten in der Warteschlange](#change-message)
-   [Zusätzliche Optionen für das Entfernen von Nachrichten aus der Warteschlange](#additional-options)
-   [Vorgehensweise: Abrufen der Warteschlangenlänge](#get-queue-length)
-   [Vorgehensweise: Löschen einer Warteschlange](#delete-queue)
-   [Nächste Schritte](#next-steps)

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

Erstellen eines Azure-Speicherkontos
------------------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Erstellen einer PHP-Anwendung
-----------------------------

Die einzige Voraussetzung für das Erstellen einer PHP-Anwendung, die auf den Azure-Warteschlangendienst zugreift, ist das Referenzieren von Klassen des Azure-SDK für PHP aus dem Code heraus. Sie können die Anwendung mit beliebigen Entwicklungstools erstellen, unter anderem auch mit Notepad.

In diesem Leitfaden verwenden Sie Funktionen des Warteschlangendienstes, die lokal innerhalb einer PHP-Anwendung oder im Code, der innerhalb einer Azure-Webrolle, -Workerrolle oder -Website ausgeführt wird, aufgerufen werden können.

Abrufen der Azure-Clientbibliotheken
------------------------------------

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

Konfigurieren der Anwendung für den Zugriff auf den Warteschlangendienst
------------------------------------------------------------------------

Zum Verwenden der Azure-Warteschlangendienst-APIs müssen Sie Folgendes durchführen:

1.  Verweisen Sie mithilfe der [require\_once](http://www.php.net/manual/en/function.require-once.php)-Anweisung auf die Autoloaderdatei und
2.  Verweisen Sie auf alle Klassen, die Sie möglicherweise verwenden.

Das folgende Beispiel zeigt, wie die Autoloaderdatei eingeschlossen und die **ServicesBuilder**-Klasse referenziert wird.

> [WACOM.NOTE] In diesem Beispiel (und in anderen Beispielen in diesem Artikel) wird angenommen, dass Sie die PHP-Clientbibliotheken für Azure über Composer installiert haben. Falls Sie die Bibliotheken manuell oder als PEAR-Paket installiert haben, müssen Sie auf die Autoloaderdatei `WindowsAzure.php` verweisen.

    require_once 'vendor\autoload.php';
    use WindowsAzure\Common\ServicesBuilder;

In den Beispielen weiter unten wird die `require_once`-Anweisung immer angezeigt, aber nur die Klassen, die für die Ausführung des Beispiels erforderlich sind, werden referenziert.

Einrichten einer Azure-Speicherverbindung
-----------------------------------------

Um einen Client für den Azure-Warteschlangendienst zu instanziieren, benötigen Sie zunächst eine gültige Verbindungszeichenfolge. Das Format der Warteschlangendienst-Verbindungszeichenfolge lautet:

Für den Zugriff auf einen Livedienst:

    DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Für den Zugriff auf den Emulatorspeicher:

    UseDevelopmentStorage=true

Um einen Azure-Dienstclient zu erstellen, müssen Sie die **ServicesBuilder**-Klasse verwenden. Sie können:

-   Die Verbindungszeichenfolge direkt an die Klasse weitergeben oder
-   den **CloudConfigurationManager (CCM)** verwenden, um mehrere externe Quellen für die Verbindungszeichenfolge zu überprüfen:
    -   Standardmäßig verfügt sie über Unterstützung für eine externe Quelle – Umgebungsvariablen.
    -   Sie können neue Quellen durch Erweitern der **ConnectionStringSource**-Klasse hinzufügen.

Für die hier erläuterten Beispiele wird die Verbindungszeichenfolge direkt weitergegeben.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;

    $queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

Vorgehensweise: Erstellen einer Warteschlange
---------------------------------------------

Über ein **QueueRestProxy**-Objekt können Sie eine Warteschlange mit der **createQueue**-Methode erstellen. Bei der Erstellung von Warteschlangen können Sie verschiedene Optionen festlegen, was allerdings nicht erforderlich ist. (Das folgende Beispiel zeigt, wie Sie die Metadaten für eine Warteschlange festlegen.)

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\Queue\Models\CreateQueueOptions;

    // Warteschlangen-REST-Proxy erstellen.
    $queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

    // OPTIONAL: Warteschlangen-Metadaten einstellen.
    $createQueueOptions = new CreateQueueOptions();
    $createQueueOptions->addMetaData("key1", "value1");
    $createQueueOptions->addMetaData("key2", "value2");

    try {
        // Warteschlange erstellen.
        $queueRestProxy->createQueue("myqueue", $createQueueOptions);
    }
    catch(ServiceException $e){
        // Ausnahme anhand von Fehlercodes und Meldungen behandeln.
        // Fehlercodes und -meldungen sind hier verfügbar: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179446.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

> [WACOM.NOTE] Metadaten-Schlüssel unterscheiden nicht immer zwischen Groß- und Kleinschreibung. Alle Schlüssel werden vom Dienst in Kleinbuchstaben gelesen.

Vorgehensweise: Hinzufügen von Nachrichten zu einer Warteschlange
-----------------------------------------------------------------

Verwenden Sie **QueueRestProxy-\>createMessage**, um Nachrichten zu einer Warteschlange hinzuzufügen. Übergeben Sie der Methode den Warteschlangennamen, Nachrichtentext und (optionale) Nachrichtenoptionen.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\Queue\Models\CreateMessageOptions;

    // Warteschlangen-REST-Proxy erstellen.
    $queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

    try {
        // Nachricht erstellen.
        $builder = new ServicesBuilder();
        $queueRestProxy->createMessage("myqueue", "Hello World!");
    }
    catch(ServiceException $e){
        // Ausnahme anhand von Fehlercodes und Meldungen behandeln.
        // Fehlercodes und -meldungen sind hier verfügbar: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179446.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Vorgehensweise: Einsehen der nächsten Nachricht
-----------------------------------------------

Sie können einen Blick auf die Nachricht(en) am Anfang einer Warteschlange werfen, ohne diese aus der Warteschlange zu entfernen, indem Sie **QueueRestProxy-\>peekMessages** aufrufen. Die Methode **peekMessage** gibt standardmäßig eine einzelne Nachricht zurück. Sie können diesen Wert jedoch mit der Methode **PeekMessagesOptions-\>setNumberOfMessages** ändern.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\Queue\Models\PeekMessagesOptions;

    // Warteschlangen-REST-Proxy erstellen.
    $queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

    // OPTIONAL: PeekMessage-Optionen festlegen.
    $message_options = new PeekMessagesOptions();
    $message_options->setNumberOfMessages(1); // Der Standardwert ist 1.

    try {
        $peekMessagesResult = $queueRestProxy->peekMessages("myqueue", $message_options);
    }
    catch(ServiceException $e){
        // Ausnahme anhand von Fehlercodes und Meldungen behandeln.
        // Fehlercodes und -meldungen sind hier verfügbar: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179446.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $messages = $peekMessagesResult->getQueueMessages();

    // Nachrichten anzeigen.
    $messageCount = count($messages);
    if($messageCount <= 0){
        echo "There are no messages.<br />";
    }
    else{
        foreach($messages as $message)  {
            echo "Peeked message:<br />";
            echo "Message Id: ".$message->getMessageId()."<br />";
            echo "Date: ".date_format($message->getInsertionDate(), 'Y-m-d')."<br />";
            echo "Message text: ".$message->getMessageText()."<br /><br />";
        }
    }

Vorgehensweise: Entfernen der nächsten Nachricht aus der Warteschlange
----------------------------------------------------------------------

Durch diesen Code wird eine Nachricht in zwei Schritten aus der Warteschlange entfernt. Zunächst rufen Sie **QueueRestProxy-\>listMessages**, um die Nachricht unsichtbar für anderen Code zu machen, der aus der Warteschlange liest. Standardmäßig bleibt die Nachricht 30 Sekunden lang unsichtbar (falls sie in diesem Zeitraum nicht gelöscht wird, kann sie wieder aus der Warteschlange gelesen werden.). Um die Nachricht endgültig aus der Warteschlange zu entfernen, müssen Sie **QueueRestProxy-\>deleteMessage** aufrufen. Dieser zweistufige Prozess zum Entfernen von Nachrichten stellt sicher, dass eine andere Codeinstanz dieselbe Nachricht erneut abrufen kann, falls die Verarbeitung aufgrund eines Hardware- oder Softwarefehlers fehlschlägt. Der Code ruft **deleteMessage** direkt nach der Verarbeitung der Nachricht auf.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Warteschlangen-REST-Proxy erstellen.
    $queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

    // Nachricht abrufen.
    $listMessagesResult = $queueRestProxy->listMessages("myqueue");
    $messages = $listMessagesResult->getQueueMessages();
    $message = $messages[0];

    /* ---------------------
        Nachricht verarbeiten.
       --------------------- */

    // Nachrichten-Id und Pop-Beleg abrufen.
    $messageId = $message->getMessageId();
    $popReceipt = $message->getPopReceipt();

    try {
        // Nachricht löschen.
        $queueRestProxy->deleteMessage("myqueue", $messageId, $popReceipt);
    }
    catch(ServiceException $e){
        // Ausnahme anhand von Fehlercodes und Meldungen behandeln.
        // Fehlercodes und -meldungen sind hier verfügbar: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179446.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Vorgehensweise: Ändern des Inhalts von Nachrichten in der Warteschlange
-----------------------------------------------------------------------

Sie können den Inhalt einer Nachricht vor Ort in der Warteschlange ändern, indem Sie **QueueRestProxy-\>updateMessage** aufrufen. Wenn die Nachricht eine Arbeitsaufgabe darstellt, können Sie diese Funktion verwenden, um den Status der Aufgabe zu aktualisieren. Mit dem folgenden Code wird die Warteschlangennachricht mit neuem Inhalt aktualisiert und das Sichtbarkeits-Zeitlimit um weitere 60 Sekunden verlängert. Dadurch wird der mit der Nachricht verknüpfte Arbeitsstatus gespeichert, und der Client erhält eine weitere Minute zur Bearbeitung der Nachricht. Sie können diese Technik verwenden, um Workflows mit mehreren Schritten in Warteschlangennachrichten zu verfolgen, ohne von vorn beginnen zu müssen, wenn ein Verarbeitungsschritt aufgrund eines Hardware- oder Softwarefehlers fehlschlägt. In der Regel behalten Sie auch die Anzahl der Wiederholungen bei, und wenn die Nachricht mehr als n Mal wiederholt wurde, wird sie gelöscht. Dies verhindert, dass eine Nachricht bei jeder Verarbeitung einen Anwendungsfehler auslöst.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;   

    // Warteschlangen-REST-Proxy erstellen.
    $queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

    // Nachricht abrufen.
    $listMessagesResult = $queueRestProxy->listMessages("myqueue");
    $messages = $listMessagesResult->getQueueMessages();
    $message = $messages[0];

    // Neue Nachrichteneigenschaften definieren.
    $new_message_text = "New message text.";
    $new_visibility_timeout = 5; // Gemessen in Sekunden. 

    // Nachrichten-Id und Pop-Beleg abrufen.
    $messageId = $message->getMessageId();
    $popReceipt = $message->getPopReceipt();

    try {
        // Nachricht ändern.
        $queueRestProxy->updateMessage("myqueue", 
                                    $messageId, 
                                    $popReceipt, 
                                    $new_message_text, 
                                    $new_visibility_timeout);
    }
    catch(ServiceException $e){
        // Ausnahme anhand von Fehlercodes und Meldungen behandeln.
        // Fehlercodes und -meldungen sind hier verfügbar: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179446.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Zusätzliche Optionen für das Entfernen von Nachrichten aus der Warteschlange
----------------------------------------------------------------------------

Es gibt zwei Möglichkeiten, wie Sie das Abrufen von Nachrichten aus der Warteschlange anpassen können. Erstens können Sie einen Nachrichtenstapel abrufen (bis zu 32). Zweitens können Sie das Sichtbarkeits-Zeitlimit verkürzen oder verlängern, sodass der Code mehr oder weniger Zeit zur vollständigen Verarbeitung jeder Nachricht benötigt. Im folgenden Codebeispiel wird **getMessages** verwendet, um 16 Nachrichten mit einem Aufruf abzurufen. Anschließend wird jede Nachricht mithilfe einer **for**-Schleife verarbeitet. Außerdem wird das Unsichtbarkeits-Zeitlimit auf fünf Minuten pro Nachricht festgelegt.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\Queue\Models\ListMessagesOptions;

    // Warteschlangen-REST-Proxy erstellen.
    $queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

    // ListMessage-Optionen einstellen. 
    $message_options = new ListMessagesOptions();
    $message_options->setVisibilityTimeoutInSeconds(300); 
    $message_options->setNumberOfMessages(16);

    // Nachrichten abrufen.
    try{
        $listMessagesResult = $queueRestProxy->listMessages("myqueue", 
                                                         $message_options); 
        $messages = $listMessagesResult->getQueueMessages(); 

        foreach($messages as $message){
            
            /* ---------------------
                Nachricht verarbeiten.
            --------------------- */
        
        // Nachrichten-Id und Pop-Beleg abrufen.
            $messageId = $message->getMessageId();
            $popReceipt = $message->getPopReceipt();
            
            // Nachricht löschen.
            $queueRestProxy->deleteMessage("myqueue", $messageId, $popReceipt);   
        }
    }
    catch(ServiceException $e){
        // Ausnahme anhand von Fehlercodes und Meldungen behandeln.
        // Fehlercodes und -meldungen sind hier verfügbar: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179446.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Vorgehensweise: Abrufen der Warteschlangenlänge
-----------------------------------------------

Sie können die Anzahl der Nachrichten in einer Warteschlange schätzen lassen. Die Methode **QueueRestProxy-\>getQueueMetadata** ruft Metadaten zur Warteschlange vom Warteschlangendienst ab. Rufen Sie die Methode **getApproximateMessageCount** auf das zurückgegebene Objekt auf, um die Anzahl der Nachrichten in der Warteschlange abzurufen. Die Anzahl ist nur ein ungefährer Wert, da seit der Antwort des Warteschlangendienstes möglicherweise bereits Nachrichten hinzugefügt oder gelöscht wurden.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Warteschlangen-REST-Proxy erstellen.
    $queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

    try {
        // Warteschlangen-Metadaten abrufen.
        $queue_metadata = $queueRestProxy->getQueueMetadata("myqueue");
        $approx_msg_count = $queue_metadata->getApproximateMessageCount();
    }
    catch(ServiceException $e){
        // Ausnahme anhand von Fehlercodes und Meldungen behandeln.
        // Fehlercodes und -meldungen sind hier verfügbar: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179446.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    echo $approx_msg_count;

Vorgehensweise: Löschen einer Warteschlange
-------------------------------------------

Zum Löschen einer Warteschlange und aller darin enthaltenen Nachrichten rufen Sie die Methode **QueueRestProxy-\>deleteQueue** auf.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Warteschlangen-REST-Proxy erstellen.
    $queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

    try {
        // Warteschlange löschen.
        $queueRestProxy->deleteQueue("myqueue");
    }
    catch(ServiceException $e){
        // Ausnahme anhand von Fehlercodes und Meldungen behandeln.
        // Fehlercodes und -meldungen sind hier verfügbar: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179446.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Nächste Schritte
----------------

Da Sie jetzt die Grundlagen des Azure-Warteschlangendiensts erlernt haben, folgen Sie diesem Link, um zu erfahren, wie Sie komplexere Speicheraufgaben ausführen können.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Speichern und Zugreifen auf Daten in Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx)
-   Besuchen Sie den Blog des Azure-Speicherteams: <http://blogs.msdn.com/b/windowsazurestorage/>;

