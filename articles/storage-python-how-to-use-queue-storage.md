<properties 
	pageTitle="Verwenden des Warteschlangendiensts (Python) | Microsoft Azure" 
	description="Erfahren Sie, wie Sie den Azure-Warteschlangendienst zum Erstellen und Löschen von Warteschlangen sowie zum Einfügen, Abrufen und Löschen von Nachrichten verwenden. Die Beispiele wurden in Python geschrieben." 
	services="storage" 
	documentationCenter="python" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="09/19/2014" 
	ms.author="robmcm"/>



# Verwenden des Warteschlangenspeicherdiensts aus Python
Dieser Leitfaden demonstriert die Durchführung häufiger Szenarien mit dem Azure-Warteschlangenspeicherdienst. Die Beispiele wurden mit der Python-API geschrieben. Zu den Szenarien gehören das **Einfügen**, **Einsehen**, **Abrufen** und **Löschen** von Warteschlangennachrichten sowie das **Erstellen und Löschen von Warteschlangen**. Weitere Informationen zu Warteschlangen finden Sie im Abschnitt [Nächste Schritte][].

## Inhaltsverzeichnis

[Was ist Warteschlangenspeicherung?][]   
 [Konzepte][]   
 [Erstellen eines Azure-Speicherkontos][]   
 [Vorgehensweise: Erstellen einer Warteschlange][]   
 [Vorgehensweise: Einfügen einer Nachricht in eine Warteschlange][]   
 [Vorgehensweise: Einsehen der nächsten Nachricht][]   
 [Vorgehensweise: Entfernen der nächsten Nachricht aus der Warteschlange][]   
 [Vorgehensweise: Ändern des Inhalts von Nachrichten in der Warteschlange][]   
 [Vorgehensweise: Zusätzliche Optionen für das Entfernen von Nachrichten aus der Warteschlange][]   
 [Vorgehensweise: Abrufen der Warteschlangenlänge][]   
 [Vorgehensweise: Löschen einer Warteschlange][]   
 [Nächste Schritte][]

[AZURE.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

## <a name="create-account"> </a>Erstellen eines Azure-Speicherkontos
[AZURE.INCLUDE [create-storage-account](../includes/create-storage-account.md)]


**Hinweis:** Informationen zur Installation von Python oder den Clientbibliotheken finden Sie im [Python-Installationshandbuch](../python-how-to-install/).

## <a name="create-queue"> </a>Vorgehensweise: Erstellen einer Warteschlange

Das Objekt **QueueService** ermöglicht es Ihnen, mit Warteschlangen zu arbeiten. Der folgende Code erstellt ein **QueueService**-Objekt. Fügen Sie am Anfang jeder Python-Datei, in der Sie programmgesteuert auf Azure-Speicher zugreifen möchten, den folgenden Code hinzu:

	from azure.storage import QueueService

Der folgende Code erstellt ein **QueueService**-Objekt unter Verwendung des Speicherkontonamens und Kontoschlüssels. Ersetzen Sie 'myaccount' und 'mykey' durch das tatsächliche Konto und den tatsächlichen Schlüssel.

	queue_service = QueueService(account_name='myaccount', account_key='mykey')

	queue_service.create_queue('taskqueue')


## <a name="insert-message"> </a>Vorgehensweise: Einfügen einer Nachricht in eine Warteschlange

Zum Einfügen einer Nachricht in eine Warteschlange verwenden Sie die **put\_message**-Methode, um eine neue Nachricht zu erstellen und zur Warteschlange hinzuzufügen.

	queue_service.put_message('taskqueue', 'Hello World')


## <a name="peek-message"> </a>Vorgehensweise: Einsehen der nächsten Nachricht

Sie die Nachricht am Anfang einer Warteschlange einsehen, ohne sie aus der Warteschlange zu entfernen, indem Sie die Methode **peek\_messages** aufrufen. Standardmäßig ruft
**peek\_messages** nur eine einzige Nachricht ab.

	messages = queue_service.peek_messages('taskqueue')
	for message in messages:
		print(message.message_text)


## <a name="get-message"> </a>Vorgehensweise: Entfernen der nächsten Nachricht aus der Warteschlange

Dieser Code entfernt eine Nachricht in zwei Schritten aus der Warteschlange. Wenn Sie **get\_messages** aufrufen, wird standardmäßig die nächste Nachricht aus der Warteschlange abgerufen. Eine von **get\_messages** zurückgegebene Nachricht ist für anderen Code nicht mehr sichtbar, der Nachrichten aus dieser Warteschlange liest. Standardmäßig bleibt die Nachricht 30 Sekunden lang unsichtbar. Um die Nachricht endgültig aus der Warteschlange zu entfernen, müssen Sie außerdem **delete\_message** aufrufen. Dieser zweistufige Prozess zum Entfernen von Nachrichten stellt sicher, dass eine andere Codeinstanz dieselbe Nachricht erneut abrufen kann, falls die Verarbeitung aufgrund eines Hardware- oder Softwarefehlers fehlschlägt. Der Code ruft **delete\_message** direkt nach der Verarbeitung der Nachricht auf.

	messages = queue_service.get_messages('taskqueue')
	for message in messages:
		print(message.message_text)
		queue_service.delete_message('taskqueue', message.message_id, message.pop_receipt)


## <a name="change-contents"> </a>Vorgehensweise: Ändern des Inhalts von Nachrichten in der Warteschlange

Sie können den Inhalt einer Nachricht vor Ort in der Warteschlange ändern. Wenn die Nachricht eine Arbeitsaufgabe darstellt, können Sie diese Funktion verwenden, um den Status der Aufgabe zu aktualisieren. Der folgende Code verwendet die **update\_message**
-Methode zum Aktualisieren einer Nachricht.

	messages = queue_service.get_messages('taskqueue')
	for message in messages:
		queue_service.update_message('taskqueue', message.message_id, 'Hello World Again', message.pop_receipt, 0)

## <a name="advanced-get"> </a>Vorgehensweise: Zusätzliche Optionen für das Entfernen von Nachrichten aus der Warteschlange

Es gibt zwei Möglichkeiten, wie Sie das Abrufen von Nachrichten aus der Warteschlange anpassen können. Erstens können Sie einen Nachrichtenstapel abrufen (bis zu 32). Zweitens können Sie das Unsichtbarkeits-Zeitlimit verkürzen oder verlängern, sodass der Code mehr oder weniger Zeit zur vollständigen Verarbeitung jeder Nachricht benötigt. Im folgenden Codebeispiel wird **get\_messages** verwendet, um 16 Nachrichten mit einem Aufruf abzurufen. Anschließend werden die
Nachrichten in einer Schleife verarbeitet. Außerdem wird das Unsichtbarkeits-Zeitlimit auf fünf Minuten pro Nachricht festgelegt.

	messages = queue_service.get_messages('taskqueue', numofmessages=16, visibilitytimeout=5*60)
	for message in messages:
		print(message.message_text)
		queue_service.delete_message('taskqueue', message.message_id, message.pop_receipt)

## <a name="get-queue-length"> </a>Vorgehensweise: Abrufen der Warteschlangenlänge

Sie können die Anzahl der Nachrichten in einer Warteschlange schätzen lassen. Die **get\_queue\_metadata**-Methode fordert den Warteschlangendienst auf, Metadaten über die Warteschlange zurückzugeben. **x-ms-approximate-messages-count** sollte als Index für das zurückgegebene Wörterbuch verwendet werden, um die Anzahl zu finden.
Die Anzahl ist nur ein ungefährer Wert, da seit der Antwort des
Warteschlangendienstes möglicherweise bereits Nachrichten hinzugefügt oder gelöscht wurden.

	queue_metadata = queue_service.get_queue_metadata('taskqueue')
	count = queue_metadata['x-ms-approximate-messages-count']

## <a name="delete-queue"> </a>Vorgehensweise: Löschen einer Warteschlange

Zum Löschen einer Warteschlange und aller darin enthaltenen Nachrichten rufen Sie die Methode **delete\_queue** auf.

	queue_service.delete_queue('taskqueue')

## <a name="next-steps"> </a>Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen der Warteschlangenspeicherung vertraut gemacht haben, folgen Sie diesen Links, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Speichern und Zugreifen auf Daten in Azure][]
-   Besuchen Sie den [Azure Storage-Teamblog][].

  [Nächste Schritte]: #next-steps
  [Was ist Warteschlangenspeicherung?]: #what-is
  [Konzepte]: #concepts
  [Erstellen eines Azure-Speicherkontos]: #create-account
  [Vorgehensweise: Erstellen einer Warteschlange]: #create-queue
  [Vorgehensweise: Einfügen einer Nachricht in eine Warteschlange]: #insert-message
  [Vorgehensweise: Einsehen der nächsten Nachricht]: #peek-message
  [Vorgehensweise: Entfernen der nächsten Nachricht aus der Warteschlange]: #get-message
  [Vorgehensweise: Ändern des Inhalts von Nachrichten in der Warteschlange]: #change-contents
  [Vorgehensweise: Zusätzliche Optionen für das Entfernen von Nachrichten aus der Warteschlange]: #advanced-get
  [Vorgehensweise: Abrufen der Warteschlangenlänge]: #get-queue-length
  [Vorgehensweise: Löschen einer Warteschlange]: #delete-queue
  [Speichern und Zugreifen auf Daten in Azure]: http://msdn.microsoft.com/de-de/library/windowsazure/gg433040.aspx
  [Azure Storage-Teamblog]: http://blogs.msdn.com/b/windowsazurestorage/

<!--HONumber=42-->
