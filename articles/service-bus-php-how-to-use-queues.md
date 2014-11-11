<properties linkid="develop-php-how-to-guides-service-bus-queues" urlDisplayName="Service Bus Queues" pageTitle="How to use Service Bus queues (PHP) - Azure" metaKeywords="Azure Service Bus queues, Azure queues, Azure messaging, Azure queues PHP" description="Learn how to use Service Bus queues in Azure. Code samples written in PHP." metaCanonical="" services="service-bus" documentationCenter="PHP" title="How to Use Service Bus Queues" authors="" solutions="" manager="" editor="" />

Verwenden von Service Bus-Warteschlangen
========================================

In diesem Leitfaden erfahren Sie, wie Sie die Service Bus-Warteschlangen mit PHP verwenden. Die Beispiele wurden in PHP geschrieben und verwenden das [Azure-SDK für PHP](http://go.microsoft.com/fwlink/?LinkId=252473). Die Szenarien behandeln die Themen **Erstellen von Warteschlangen**, **Senden und Empfangen von Nachrichten** und **Löschen von Warteschlangen**.

Inhaltsverzeichnis
------------------

-   [Was sind Service Bus-Warteschlangen?](#what-are-service-bus-queues)
-   [Erstellen eines Dienstnamespaces](#create-a-service-namespace)
-   [Abrufen der Standard-Anmeldeinformationen für den Namespace](#obtain-default-credentials)
-   [Erstellen einer PHP-Anwendung](#CreateApplication)
-   [Abrufen der Azure-Clientbibliotheken](#GetClientLibrary)
-   [Konfigurieren Ihrer Anwendung für die Verwendung von Service Bus](#ConfigureApp)
-   [Gewusst wie: Erstellen einer Warteschlange](#CreateQueue)
-   [Gewusst wie: Senden von Nachrichten an eine Warteschlange](#SendMessages)
-   [Gewusst wie: Empfangen von Nachrichten aus einer Warteschlange](#ReceiveMessages)
-   [Gewusst wie: Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten](#HandleCrashes)
-   [Nächste Schritte](#NextSteps)

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

Erstellen einer PHP-Anwendung
-----------------------------

Die einzige Voraussetzung für das Erstellen einer PHP-Anwendung, die auf den Azure-Blob-Dienst zugreift, ist das Referenzieren von Klassen im [Azure-SDK für PHP](http://go.microsoft.com/fwlink/?LinkId=252473) aus dem Code heraus. Sie können beliebige Entwicklungstools zum Erstellen Ihrer Anwendung verwenden, einschließlich Editor.

> [WACOM.NOTE] in Ihrer PHP-Installation muss außerdem die [OpenSSL-Erweiterung](http://php.net/openssl) installiert und aktiviert sein.

In diesem Leitfaden verwenden Sie Dienstfunktionen, die lokal innerhalb einer PHP-Anwendung oder im Code, der innerhalb einer Azure-Webrolle, -Workerrolle oder -Website ausgeführt wird, aufgerufen werden können.

Abrufen der Azure-Clientbibliotheken
------------------------------------

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

Konfigurieren Ihrer Anwendung für die Verwendung von Service Bus
----------------------------------------------------------------

Um die APIs für Azure Service Bus-Warteschlangen zu verwenden, benötigen Sie Folgendes:

1.  Verweisen Sie mithilfe der [require\_once](http://php.net/require_once)-Anweisung auf die Autoloaderdatei und
2.  Verweisen Sie auf alle Klassen, die Sie möglicherweise verwenden.

Das folgende Beispiel zeigt, wie die Autoloaderdatei eingeschlossen und die **ServicesBuilder**-Klasse referenziert wird.

> [WACOM.NOTE] 
> In diesem Beispiel (und in anderen Beispielen in diesem Artikel) wird angenommen, dass Sie die PHP-Clientbibliotheken für Azure über Composer installiert haben. Falls Sie die Bibliotheken manuell oder als PEAR-Paket installiert haben, müssen Sie auf die Autoloaderdatei `WindowsAzure.php` verweisen.

    require_once 'vendor\autoload.php';
    use WindowsAzure\Common\ServicesBuilder;

In den Beispielen weiter unten wird die `require_once`-Anweisung immer angezeigt, aber nur die Klassen, die für die Ausführung des Beispiels erforderlich sind, werden referenziert.

Einrichten einer Azure Service Bus-Verbindung
---------------------------------------------

Um einen Azure Service Bus-Client zu instanzieren, benötigen Sie zunächst eine gültige Verbindungszeichenfolge mit dem folgenden Format:

    Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]

Wobei der Endpunkt normalerweise das Format `https://[yourNamespace].servicebus.windows.net` hat.

Um einen Azure-Dienstclient zu erstellen, müssen Sie die **ServicesBuilder**-Klasse verwenden. Sie können:

-   Die Verbindungszeichenfolge direkt an die Klasse weitergeben oder
-   den **CloudConfigurationManager (CCM)** verwenden, um mehrere externe Quellen für die Verbindungszeichenfolge zu überprüfen:
    -   Standardmäßig verfügt sie über Unterstützung für eine externe Quelle – Umgebungsvariablen
    -   Sie können neue Quellen durch Erweitern der **ConnectionStringSource**-Klasse hinzufügen

Für die hier erläuterten Beispiele wird die Verbindungszeichenfolge direkt weitergegeben.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;

    $connectionString = "Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]";

    $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

Gewusst wie: Erstellen einer Warteschlange
------------------------------------------

Sie können Verwaltungsvorgänge für Service Bus-Warteschlangen über die Klasse **ServiceBusRestProxy** durchführen. Ein **ServiceBusRestProxy**-Objekt wird über die **ServicesBuilder::createServiceBusService**-Factorymethode mit einer entsprechenden Verbindungszeichenfolge erstellt, welche die Token-Berechtigungen für deren Verwaltung kapselt.

Das folgende Beispiel zeigt, wie Sie einen **ServiceBusRestProxy** instanziieren und **ServiceBusRestProxy-\>createQueue** aufrufen, um eine Warteschlange namens `myqueue` in einem Dienstnamespace namens `MySBNamespace` erstellen:

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\ServiceBus\Models\QueueInfo;

    // Service Bus-REST-Proxy erstellen.
        $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

    try {
        $queueInfo = new QueueInfo("myqueue");
        
        // Warteschlange erstellen.
        $serviceBusRestProxy->createQueue($queueInfo);
    }
    catch(ServiceException $e){
        // Ausnahme anhand von Fehlercodes und Meldungen behandeln.
        // Fehlercodes und Nachrichten finden Sie unter: 
        // http://msdn.microsoft.com/de-de/library/windowsazure/dd179357
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

> [WACOM.NOTE] 
> Sie können die **listQueues**-Methode von **ServiceBusRestProxy**-Objekten verwenden, um zu prüfen, ob eine Warteschlange mit einem spezifischen Namen bereits innerhalb eines Dienstnamespace vorhanden ist.

Gewusst wie: Senden von Nachrichten an eine Warteschlange
---------------------------------------------------------

Um eine Nachricht an eine Service Bus-Warteschlange zu senden, ruft Ihre Anwendung die **ServiceBusRestProxy-\>sendQueueMessage**-Methode auf. Der folgende Code zeigt, wie Sie eine Nachricht an die zuvor erstellte Warteschlange `myqueue` im Dienstnamespace `MySBNamespace` schicken können.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\ServiceBus\models\BrokeredMessage;

    // Service Bus-REST-Proxy erstellen.
    $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
    try {
        // Nachricht erstellen.
        $message = new BrokeredMessage();
        $message->setBody("my message");

        // Nachricht senden.
        $serviceBusRestProxy->sendQueueMessage("myqueue", $message);
    }
    catch(ServiceException $e){
        // Ausnahme anhand von Fehlercodes und Meldungen behandeln.
        // Fehlercodes und Nachrichten finden Sie unter: 
        // http://msdn.microsoft.com/de-de/library/windowsazure/hh780775
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Die Nachrichten, die an die Service Bus-Warteschlangen gesendet werden (und von diesen empfangen werden), sind Instanzen der Klasse **BrokeredMessage**. **BrokeredMessage**-Objekte enthalten eine Reihe von Standardmethoden (wie etwa **getLabel** und **getTimeToLive**, **setLabel** und **setTimeToLive**) sowie Eigenschaften für die Aufnahme benutzerdefinierter anwendungsspezifischer Eigenschaften und einen Körper beliebiger Anwendungsdaten.

Service Bus-Warteschlangen unterstützen eine maximale Nachrichtengröße von 256 KB (der Header, der die standardmäßigen und die benutzerdefinierten Anwendungseigenschaften enthält, kann eine maximale Größe von 64 KB haben). Bei der Anzahl der Nachrichten, die in einer Warteschlange aufgenommen werden können, besteht keine Beschränkung. Allerdings gilt eine Deckelung bei der Gesamtgröße der in einer Warteschlange aufzunehmenden Nachrichten. Die Obergrenze für die Warteschlangengröße beträgt 5 GB.

Gewusst wie: Empfangen von Nachrichten aus einer Warteschlange
--------------------------------------------------------------

Der einfachste Weg zum Empfangen von Nachrichten aus Warteschlangen ist die **ServiceBusRestProxy-\>receiveQueueMessage**-Methode. Nachrichten können auf zwei verschiedene Arten empfangen werden: **ReceiveAndDelete** (Standard) und **PeekLock**.

Bei Verwendung des **ReceiveAndDelete**-Modus ist der Nachrichtenempfang ein Single-Shot-Vorgang. Dies bedeutet, wenn Service Bus eine Leseanforderung für eine Nachricht in eine Warteschlange erhält, wird die Nachricht als verarbeitet gekennzeichnet und an die Anwendung zurück gesendet. **Der ReceiveAndDelete**-Modus ist das einfachste Modell. Es wird am besten für Szenarien eingesetzt, bei denen es eine Anwendung tolerieren kann, wenn eine Nachricht bei Auftreten eines Fehlers nicht verarbeitet wird. Um dieses Verfahren zu verstehen, stellen Sie sich ein Szenario vor, in dem der Consumer die Empfangsanforderung ausstellt und dann abstürzt, bevor diese verarbeitet wird. Da Service Bus die Nachricht als verwendet markiert hat, wird er jene Nachricht auslassen, die vor dem Absturz verwendet wurde, wenn die Anwendung neu startet und erneut mit der Verwendung von Nachrichten beginnt.

Im **PeekLock**-Modus ist der Nachrichtenempfang zweistufig. Dadurch können Anwendungen unterstützt werden, die das Auslassen bzw. Fehlen von Nachrichten nicht zulassen können. Wenn Service Bus eine Anfrage erhält, ermittelt der Dienst die nächste zu verarbeitende Nachricht, sperrt diese, um zu verhindern, dass andere Consumer sie erhalten, und sendet sie dann an die Anwendung zurück. Nachdem die Anwendung die Verarbeitung der Nachricht abgeschlossen hat (oder sie zwecks zukünftiger Verarbeitung zuverlässig gespeichert hat), führt Sie die zweite Phase des Empfangsprozesses durch Aufruf von **ServiceBusRestProxy-\>deleteMessage** durch. Wenn Service Bus den **deleteMessage**-Aufruf erkennt, markiert er die Nachricht als verwendet und entfernt sie aus der Warteschlange.

Das folgende Beispiel zeigt, wie eine Nachricht mit dem nicht standardmäßig verwendeten **PeekLock**-Modus empfangen und verarbeitet werden kann.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\ServiceBus\models\ReceiveMessageOptions;

    // Service Bus-REST-Proxy erstellen.
    $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
    try {
        // PeekLock als Empfangsmodus einstellen (Standard ist ReceiveAndDelete).
        $options = new ReceiveMessageOptions();
        $options->setPeekLock();
        
        // Nachricht empfangen.
        $message = $serviceBusRestProxy->receiveQueueMessage("myqueue", $options);
        echo "Body: ".$message->getBody()."<br />";
        echo "MessageID: ".$message->getMessageId()."<br />";
        
        /*---------------------------
            Nachricht verarbeiten.
        ----------------------------*/
        
        // Nachricht löschen. Nicht erforderlich, falls Peek Lock nicht eingestellt wurde.
        echo "Message deleted.<br />";
        $serviceBusRestProxy->deleteMessage($message);
    }
    catch(ServiceException $e){
        // Ausnahme anhand von Fehlercodes und Meldungen behandeln.
        // Fehlercodes und Nachrichten finden Sie unter:
        // http://msdn.microsoft.com/de-de/library/windowsazure/hh780735
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Gewusst wie: Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten
--------------------------------------------------------------------------

Service Bus stellt Funktionen zur Verfügung, die Ihnen bei der ordnungsgemäßen Behebung von Fehlern in der Anwendung oder bei Problemen beim Verarbeiten einer Nachricht helfen. Wenn eine empfangene Anwendung eine Nachricht aus einem beliebigen Grund nicht verarbeiten kann, kann sie die **unlockMessage**-Methode für die empfangene Nachricht aufrufen (anstelle der **deleteMessage**-Methode). Dies führt dazu, dass Service Bus die Nachricht innerhalb der Warteschlange entsperrt und verfügbar macht, damit sie erneut empfangen werden kann, und zwar entweder durch dieselbe verarbeitende Anwendung oder durch eine andere verarbeitende Anwendung.

Zudem wird einer in der Warteschlange gesperrten Anwendung ein Zeitlimit zugeordnet. Wenn die Anwendung die Nachricht vor Ablauf des Sperrzeitlimits nicht verarbeiten kann (zum Beispiel wenn die Anwendung abstürzt), entsperrt Service Bus die Nachricht automatisch und macht sie verfügbar, um erneut empfangen zu werden.

Falls die Anwendung nach der Verarbeitung der Nachricht aber vor Ausgabe der **deleteMessage**-Anforderung abstürzt, wird die Nachricht wieder an die Anwendung zugestellt, wenn diese neu gestartet wird. Dies wird häufig als **At Least Once Processing** (Verarbeitung mindestens einmal) bezeichnet und bedeutet, dass jede Nachricht mindestens einmal verarbeitet wird, wobei dieselbe Nachricht in bestimmten Situationen möglicherweise erneut zugestellt wird. Wenn eine doppelte Verarbeitung in dem Szenario nicht zulässig ist, sollte Ihre Anwendung zusätzliche Logik für den Umgang mit der Zustellung doppelter Nachrichten enthalten. Dies wird häufig durch die Verwendung der **getMessageId**-Methode der Nachricht erzielt, die über mehrere Zustellungsversuche hinweg konstant bleibt.

Nächste Schritte
----------------

Nachdem Sie nun mit den Grundlagen der Service Bus-Warteschlangen vertraut sind, finden Sie weitere Informationen im MSDN-Thema [Warteschlangen, Themen und Abonnements](http://msdn.microsoft.com/de-de/library/windowsazure/hh367516.aspx).

