<properties 
	pageTitle="Verwenden des Warteschlangendiensts (PHP) | Microsoft Azure" 
	description="Erfahren Sie, wie Sie mit dem Azure-Warteschlangendienst Warteschlangen erstellen und löschen sowie Nachrichten einfügen, abrufen und löschen können. Die Beispiele wurden in PHP geschrieben." 
	documentationCenter="php" 
	services="storage" 
	authors="tfitzmac" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="tomfitz"/>

# Verwenden des Warteschlangendienstes aus PHP

Dieses Handbuch demonstriert Ihnen allgemeine Szenarien unter Verwendung des Warteschlangendienstes in Azure. Die Beispiele wurden unter Verwendung von Klassen des Windows SDK für PHP geschrieben. Die behandelten Szenarien umfassen das **Einfügen**, **Einsehen**, **Abrufen**, und **Löschen** von Warteschlangennachrichten sowie das **Erstellen und Löschen von Warteschlangen**. Weitere Informationen zu Warteschlangen finden Sie unter [Nächste Schritte](#NextSteps) .

##Inhaltsverzeichnis

* [Was ist die Warteschlangenspeicherung](#what-is)
* [Konzepte](#concepts)
* [Erstellen eines Azure-Speicherkontos](#create-account)
* [Erstellen einer PHP-Anwendung](#create-app)
* [Konfigurieren der Anwendung für den Warteschlangendienst](#configure-app)
* [Einrichten einer Azure-Speicherverbindung](#connection-string)
* [Vorgehensweise: Erstellen einer Warteschlange](#create-queue)
* [Vorgehensweise: Nachricht zu einer Warteschlange hinzufügen](#add-message)
* [Vorgehensweise: Einsehen der nächsten Nachricht](#peek-message)
* [Vorgehensweise: Entfernen der nächsten Nachricht](#dequeue-message)
* [Vorgehensweise: Ändern des Inhalts von Nachrichten in der Warteschlange](#change-message)
* [Zusätzliche Optionen für Nachrichten aus der Warteschlange](#additional-options)
* [Vorgehensweise: Abrufen der Warteschlangenlänge](#get-queue-length)
* [Vorgehensweise: Löschen einer Warteschlange](#delete-queue)
* [Nächste Schritte](#next-steps)

[AZURE.INCLUDE [storage-queue-concepts-include](../includes/storage-queue-concepts-include.md)]

<h2><a id="create-account"></a>Erstellen eines Azure-Speicherkontos</h2>

[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]

<h2><a id="create-app"></a>Erstellen einer PHP-Anwendung</h2>

Die einzige Voraussetzung für das Erstellen einer PHP-Anwendung, die auf den Azure-Warteschlangendienst zugreift, ist das Referenzieren von Klassen des Azure-SDK für PHP aus dem Code heraus. Sie können die Anwendung mit beliebigen Entwicklungstools erstellen, unter anderem auch mit Notepad.

In diesem Leitfaden verwenden Sie Funktionen des Warteschlangendiensts, die lokal innerhalb einer PHP-Anwendung oder im Code, der innerhalb einer Azure-Webrolle, -Workerrolle oder -Website ausgeführt wird, aufgerufen werden können.

<h2><a id="GetClientLibrary"></a>Abrufen der Azure-Clientbibliotheken</h2>

[AZURE.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

<h2><a id="configure-app"></a>Konfigurieren der Anwendung für den Zugriff auf den Warteschlangendienst</h2>

Zum Verwenden der Azure-Warteschlangendienst-APIs müssen Sie Folgendes durchführen:

1. Verweisen Sie mithilfe der [require_once][require_once]-Anweisung auf die Autoloaderdatei, und
2. Verweisen Sie auf alle Klassen, die Sie möglicherweise verwenden.

Das folgende Beispiel zeigt, wie die Autoloaderdatei integriert werden, und auf die **ServicesBuilder**-Klasse verwiesen werden kann.

> [AZURE.NOTE]
> In diesem Beispiel (und in anderen Beispielen in diesem Artikel) wird angenommen, dass Sie die PHP-Clientbibliotheken für Azure über Composer installiert haben. Wenn Sie die Bibliotheken manuell oder als PEAR-Paket installiert haben, müssen Sie auf die `WindowsAzure.php` Autoloaderdatei verweisen.

	require_once 'vendor\autoload.php';
	use WindowsAzure\Common\ServicesBuilder;


In den Beispielen weiter unten wird die `require_once`-Anweisung immer angezeigt. Jedoch wird nur auf die für die Ausführung des Beispiels erforderlichen Klassen verwiesen.

<h2><a id="connection-string"></a>Einrichten einer Azure-Speicherverbindung</h2>

Um einen Client für den Azure-Warteschlangendienst zu instanziieren, benötigen Sie zunächst eine gültige Verbindungszeichenfolge. Das Format der Warteschlangendienst-Verbindungszeichenfolge lautet:

Für den Zugriff auf einen Livedienst:

	DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Für den Zugriff auf den Emulatorspeicher:

	UseDevelopmentStorage=true


Um einen Azure-Dienstclient zu erstellen, verwenden Sie die **ServicesBuilder**-Klasse. Sie können:

* Die Verbindungszeichenfolge direkt an die Klasse weitergeben oder
* verwenden Sie den **CloudConfigurationManager (CCM)**, um mehrere externe Quellen für die Verbindungszeichenfolge zu überprüfen:
	* Standardmäßig wird eine externe Quelle unterstützt. - Umgebungsvariablen
	* Neue Datenquellen können über die Erweiterung der **ConnectionStringSource**-Klasse hinzugefügt werden.

Für die hier erläuterten Beispiele wird die Verbindungszeichenfolge direkt weitergegeben.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;

	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);


<h2><a id="create-queue"></a>Vorgehensweise: Erstellen einer Warteschlange</h2>

Ein **QueueRestProxy**-Objekt ermöglicht Ihnen das Erstellen einer Warteschlange mit der **createQueue**-Methode. Bei der Erstellung von Warteschlangen können Sie verschiedene Optionen festlegen, was allerdings nicht erforderlich ist. (Das folgende Beispiel zeigt, wie Sie die Metadaten für eine Warteschlange festlegen.)

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Queue\Models\CreateQueueOptions;
	
	// Create queue REST proxy.
	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
	
	// OPTIONAL: Set queue metadata.
	$createQueueOptions = new CreateQueueOptions();
	$createQueueOptions->addMetaData("key1", "value1");
	$createQueueOptions->addMetaData("key2", "value2");
	
	try	{
		// Create queue.
		$queueRestProxy->createQueue("myqueue", $createQueueOptions);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179446.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

> [AZURE.NOTE]
> Metadaten-Schlüssel unterscheiden nicht immer zwischen Groß- und Kleinschreibung. Alle Schlüssel werden vom Dienst in Kleinbuchstaben gelesen.


<h2><a id="add-message"></a>Vorgehensweise: Nachricht zu einer Warteschlange hinzufügen</h2>

Um eine Nachricht zu einer Warteschlange hinzuzufügen, verwenden Sie **QueueRestProxy->createMessage**. Übergeben Sie der Methode den Warteschlangennamen, Nachrichtentext und (optionale) Nachrichtenoptionen.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Queue\Models\CreateMessageOptions;

	// Create queue REST proxy.
	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
	
	try	{
		// Create message.
		$builder = new ServicesBuilder();
		$queueRestProxy->createMessage("myqueue", "Hello World!");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179446.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

<h2><a id="peek-message"></a>Vorgehensweise: Einsehen der nächsten Nachricht</h2>

Sie können einen Blick auf eine Nachricht (oder Nachrichten) am Anfang einer Warteschlange werfen, ohne diese aus der Warteschlange zu entfernen, indem Sie **QueueRestProxy->peekMessages** aufrufen. In der Standardeinstellung gibt die **peekMessage**-Methode eine einzelne Nachricht zurück, aber Sie können diesen Wert über die **PeekMessagesOptions->setNumberOfMessages**-Methode ändern.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Queue\Models\PeekMessagesOptions;

	// Create queue REST proxy.
	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
	
	// OPTIONAL: Set peek message options.
	$message_options = new PeekMessagesOptions();
	$message_options->setNumberOfMessages(1); // Default value is 1.
	
	try	{
		$peekMessagesResult = $queueRestProxy->peekMessages("myqueue", $message_options);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179446.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
	
	$messages = $peekMessagesResult->getQueueMessages();

	// View messages.
	$messageCount = count($messages);
	if($messageCount <= 0){
		echo "There are no messages.<br />";
	}
	else{
		foreach($messages as $message)	{
			echo "Peeked message:<br />";
			echo "Message Id: ".$message->getMessageId()."<br />";
			echo "Date: ".date_format($message->getInsertionDate(), 'Y-m-d')."<br />";
			echo "Message text: ".$message->getMessageText()."<br /><br />";
		}
	}

<h2><a id="dequeue-message"></a>Vorgehensweise: Entfernen der nächsten Nachricht</h2>

Dieser Code entfernt eine Nachricht in zwei Schritten aus der Warteschlange. Zunächst rufen Sie **QueueRestProxy->listMessages** auf, wodurch die Nachricht für andere Codes nicht mehr sichtbar ist. Standardmäßig bleibt die Nachricht 30 Sekunden lang unsichtbar (falls sie in diesem Zeitraum nicht gelöscht wird, kann sie wieder aus der Warteschlange gelesen werden.). Um das Entfernen der Nachricht aus der Warteschlange abzuschließen, rufen Sie **QueueRestProxy->deleteMessage** auf. Dieser zweistufige Prozess zum Entfernen von Nachrichten stellt sicher, dass eine andere Codeinstanz dieselbe Nachricht erneut abrufen kann, falls die Verarbeitung aufgrund eines Hardware- oder Softwarefehlers fehlschlägt. Der Code ruft **deleteMessage** auf, direkt nachdem die Nachricht verarbeitet wurde.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create queue REST proxy.
	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
	
	// Get message.
	$listMessagesResult = $queueRestProxy->listMessages("myqueue");
	$messages = $listMessagesResult->getQueueMessages();
	$message = $messages[0];
	
	/* ---------------------
		Process message.
	   --------------------- */
	
	// Get message Id and pop receipt.
	$messageId = $message->getMessageId();
	$popReceipt = $message->getPopReceipt();
	
	try	{
		// Delete message.
		$queueRestProxy->deleteMessage("myqueue", $messageId, $popReceipt);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179446.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

<h2><a id="change-message"></a>Vorgehensweise: Ändern des Inhalts von Nachrichten in der Warteschlange</h2>

Sie können den Inhalt einer Nachricht direkt in der Warteschlange ändern, indem Sie **QueueRestProxy->updateMessage** aufrufen. Wenn die Nachricht eine Arbeitsaufgabe darstellt, können Sie diese Funktion verwenden, um den Status der Aufgabe zu aktualisieren. Mit dem folgenden Code wird die Warteschlangennachricht mit neuem Inhalt aktualisiert und das Sichtbarkeits-Zeitlimit um weitere 60 Sekunden verlängert. Dadurch wird der mit der Nachricht verknüpfte Arbeitsstatus gespeichert, und der Client erhält eine weitere Minute zur Bearbeitung der Nachricht. Sie können diese Technik verwenden, um Workflows mit mehreren Schritten in Warteschlangennachrichten zu verfolgen, ohne von vorn beginnen zu müssen, wenn ein Verarbeitungsschritt aufgrund eines Hardware- oder Softwarefehlers fehlschlägt. In der Regel behalten Sie auch die Anzahl der Wiederholungen bei, und wenn die Nachricht mehr als n Mal wiederholt wurde, wird sie gelöscht. Dies verhindert, dass eine Nachricht bei jeder Verarbeitung einen Anwendungsfehler auslöst.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;	

	// Create queue REST proxy.
	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
	
	// Get message.
	$listMessagesResult = $queueRestProxy->listMessages("myqueue");
	$messages = $listMessagesResult->getQueueMessages();
	$message = $messages[0];
	
	// Define new message properties.
	$new_message_text = "New message text.";
	$new_visibility_timeout = 5; // Measured in seconds. 
	
	// Get message Id and pop receipt.
	$messageId = $message->getMessageId();
	$popReceipt = $message->getPopReceipt();
	
	try	{
		// Update message.
		$queueRestProxy->updateMessage("myqueue", 
									$messageId, 
									$popReceipt, 
									$new_message_text, 
									$new_visibility_timeout);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179446.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

<h2><a id="additional-options"></a>Zusätzliche Optionen für Nachrichten aus der Warteschlange</h2>

Es gibt zwei Möglichkeiten, wie Sie das Abrufen von Nachrichten aus der Warteschlange anpassen können. Erstens können Sie einen Nachrichtenstapel abrufen (bis zu 32). Zweitens können Sie das Sichtbarkeits-Zeitlimit verkürzen oder verlängern, sodass der Code mehr oder weniger Zeit zur vollständigen Verarbeitung jeder Nachricht benötigt. Im folgenden Codebeispiel wird die **getMessages**-Methode verwendet, um 16 Nachrichten pro Aufruf abzurufen. Anschließend wird jede Nachricht mithilfe einer **für** Schleife verarbeitet. Außerdem wird das Unsichtbarkeits-Zeitlimit auf fünf Minuten pro Nachricht festgelegt.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Queue\Models\ListMessagesOptions;

	// Create queue REST proxy.
	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
	
	// Set list message options. 
	$message_options = new ListMessagesOptions();
	$message_options->setVisibilityTimeoutInSeconds(300); 
	$message_options->setNumberOfMessages(16);
	
	// Get messages.
	try{
		$listMessagesResult = $queueRestProxy->listMessages("myqueue", 
														 $message_options); 
		$messages = $listMessagesResult->getQueueMessages(); 

		foreach($messages as $message){
			
			/* ---------------------
				Process message.
			--------------------- */
		
			// Get message Id and pop receipt.
			$messageId = $message->getMessageId();
			$popReceipt = $message->getPopReceipt();
			
			// Delete message.
			$queueRestProxy->deleteMessage("myqueue", $messageId, $popReceipt);   
		}
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179446.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

<h2><a id="get-queue-length"></a>Vorgehensweise: Abrufen der Warteschlangenlänge</h2>

Sie können die Anzahl der Nachrichten in einer Warteschlange schätzen lassen. Die **QueueRestProxy->getQueueMetadata**-Methode fordert den Warteschlangendienst auf, Metadaten der Warteschlange zurückzugeben. Das Aufrufen der **getApproximateMessageCount**-Methode für das zurückgegebene Objekt liefert die Anzahl der Nachrichten, die sich in der Warteschlange befinden. Die Anzahl ist nur ein ungefährer Wert, da seit der Antwort des Warteschlangendienstes möglicherweise bereits Nachrichten hinzugefügt oder gelöscht wurden.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create queue REST proxy.
	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
	
	try	{
		// Get queue metadata.
		$queue_metadata = $queueRestProxy->getQueueMetadata("myqueue");
		$approx_msg_count = $queue_metadata->getApproximateMessageCount();
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179446.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
	
	echo $approx_msg_count;

<h2><a id="delete-queue"></a>Vorgehensweise: Löschen einer Warteschlange</h2>

Zum Löschen einer Warteschlange und aller darin enthaltenen Nachrichten, rufen Sie die **QueueRestProxy->deleteQueue**-Methode auf.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create queue REST proxy.
	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
	
	try	{
		// Delete queue.
		$queueRestProxy->deleteQueue("myqueue");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/windowsazure/dd179446.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}


<h2><a id="next-steps"></a>Nächste Schritte</h2>

Da Sie jetzt die Grundlagen des Azure-Warteschlangendiensts erlernt haben, folgen Sie diesem Link, um zu erfahren, wie Sie komplexere Speicheraufgaben ausführen können.

- Weitere Informationen finden Sie in der MSDN-Referenz: [Speichern und Zugreifen auf Daten in Azure] []
- Besuchen Sie den Blog des Azure-Speicherteams: <http://blogs.msdn.com/b/windowsazurestorage/>

[Herunterladen]: http://go.microsoft.com/fwlink/?LinkID=252473
[require_once]: http://www.php.net/manual/en/function.require-once.php
[Azure-Verwaltungsportal]: http://manage.windowsazure.com/
[Speichern und Zugreifen auf Daten in Azure]: http://msdn.microsoft.com/library/windowsazure/gg433040.aspx
<!--HONumber=42-->
