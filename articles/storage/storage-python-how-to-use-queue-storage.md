<properties
	pageTitle="Verwenden des Warteschlangenspeichers mit Python | Microsoft Azure"
	description="Erfahren Sie, wie Sie mit dem Azure-Warteschlangendienst mit Python Warteschlangen erstellen und löschen sowie Nachrichten einfügen, abrufen und löschen können."
	services="storage"
	documentationCenter="python"
	authors="emgerner-msft"
	manager="wpickett"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="02/11/2016"
	ms.author="emgerner"/>

# Verwenden des Warteschlangenspeichers mit Python

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]

## Übersicht

In diesem Leitfaden wird die Durchführung häufiger Szenarien mit dem Azure-Warteschlangenspeicherdienst demonstriert. Die Beispiele sind in Python geschrieben und verwenden das [Microsoft Azure Storage-SDK für Python]. Zu den Szenarien gehören das **Einfügen**, **Einsehen**, **Abrufen** und **Löschen** von Warteschlangennachrichten sowie das **Erstellen und Löschen von Warteschlangen**. Weitere Informationen zu Warteschlangen finden Sie im Abschnitt [Nächste Schritte].

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Erstellen einer Warteschlange

Das Objekt **QueueService** ermöglicht es Ihnen, mit Warteschlangen zu arbeiten. Der folgende Code erstellt ein **QueueService**-Objekt. Fügen Sie am Anfang jeder Python-Datei, in der Sie programmgesteuert auf Azure-Speicher zugreifen möchten, den folgenden Code hinzu:

	from azure.storage.queue import QueueService

Der folgende Code erstellt ein **QueueService**-Objekt unter Verwendung des Speicherkontonamens und Kontoschlüssels. Ersetzen Sie „myaccount“ und „mykey“ durch Ihren Kontonamen und Schlüssel.

	queue_service = QueueService(account_name='myaccount', account_key='mykey')

	queue_service.create_queue('taskqueue')


## Einfügen einer Nachricht in eine Warteschlange

Zum Einfügen einer Nachricht in eine Warteschlange verwenden Sie die **put\_message**-Methode, um eine neue Nachricht zu erstellen und zur Warteschlange hinzuzufügen.

	queue_service.put_message('taskqueue', u'Hello World')


## Einsehen der nächsten Nachricht

Sie können einen Blick auf die Nachricht am Anfang einer Warteschlange werfen, ohne sie aus der Warteschlange zu entfernen, indem Sie die Methode **peek\_messages** aufrufen. Standardmäßig ruft **peek\_messages** nur eine einzige Nachricht ab.

	messages = queue_service.peek_messages('taskqueue')
	for message in messages:
		print(message.message_text)


## Gewusst wie: Entfernen von Nachrichten aus Warteschlangen

Dieser Code entfernt eine Nachricht in zwei Schritten aus der Warteschlange. Wenn Sie **get\_messages** aufrufen, wird standardmäßig die nächste Nachricht aus der Warteschlange abgerufen. Die für **get\_messages** zurückgegebene Nachricht ist für anderen Code nicht mehr sichtbar, der Nachrichten aus dieser Warteschlange liest. Standardmäßig bleibt die Nachricht 30 Sekunden lang unsichtbar. Um die Nachricht endgültig aus der Warteschlange zu entfernen, müssen Sie außerdem **delete\_message** aufrufen. Dieser zweistufige Prozess zum Entfernen von Nachrichten stellt sicher, dass eine andere Codeinstanz dieselbe Nachricht erneut abrufen kann, falls die Verarbeitung aufgrund eines Hardware- oder Softwarefehlers fehlschlägt. Der Code ruft **delete\_message** direkt nach der Verarbeitung der Nachricht auf.

	messages = queue_service.get_messages('taskqueue')
	for message in messages:
		print(message.content)
		queue_service.delete_message('taskqueue', message.message_id, message.pop_receipt)

Es gibt zwei Möglichkeiten, wie Sie das Abrufen von Nachrichten aus der Warteschlange anpassen können. Erstens können Sie einen Nachrichtenstapel abrufen (bis zu 32). Zweitens können Sie das Unsichtbarkeits-Zeitlimit verkürzen oder verlängern, sodass der Code mehr oder weniger Zeit zur vollständigen Verarbeitung jeder Nachricht benötigt. Das folgende Codebeispiel verwendet **get\_messages**, um 16 Nachrichten mit einem Aufruf abzurufen. Anschließend wird jede Nachricht mithilfe einer for-Schleife verarbeitet. Außerdem wird das Unsichtbarkeits-Zeitlimit auf fünf Minuten pro Nachricht festgelegt.

	messages = queue_service.get_messages('taskqueue', numofmessages=16, visibilitytimeout=5*60)
	for message in messages:
		print(message.content)
		queue_service.delete_message('taskqueue', message.message_id, message.pop_receipt)		


## Ändern des Inhalts von Nachrichten in der Warteschlange

Sie können den Inhalt einer Nachricht vor Ort in der Warteschlange ändern. Wenn die Nachricht eine Arbeitsaufgabe darstellt, können Sie diese Funktion verwenden, um den Status der Aufgabe zu aktualisieren. Der folgende Code verwendet die **update\_message**-Methode zum Aktualisieren einer Nachricht. Das Sichtbarkeitstimeout ist auf 0 festgelegt, d. h., die Nachricht wird sofort angezeigt, und der Inhalt wird aktualisiert.

	messages = queue_service.get_messages('taskqueue')
	for message in messages:
		queue_service.update_message('taskqueue', message.message_id, message.pop_receipt, 0, u'Hello World Again')

## Abrufen der Warteschlangenlänge

Sie können die Anzahl der Nachrichten in einer Warteschlange schätzen lassen. Die **get\_queue\_metadata()**-Methode ruft den Warteschlangendienst ab, um Metadaten zur Warteschlange und die **approximate\_message\_count** zurückzugeben. Die Anzahl ist nur ein ungefährer Wert, da seit der Antwort des Warteschlangendienstes möglicherweise bereits Nachrichten hinzugefügt oder gelöscht wurden.

	metadata = queue_service.get_queue_metadata('taskqueue')
	count = metadata.approximate_message_count

## Löschen von Warteschlangen

Zum Löschen einer Warteschlange und aller darin enthaltenen Nachrichten rufen Sie die Methode **delete\_queue** auf.

	queue_service.delete_queue('taskqueue')

## Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen von Queue Storage vertraut gemacht haben, lesen Sie die folgenden Artikel, um mehr zu erfahren.

- [Python Developer Center](/develop/python/)
- [REST-API für Azure-Speicherdienste](http://msdn.microsoft.com/library/azure/dd179355)
- [Azure Storage-Teamblog]
- [Microsoft Azure Storage-SDK für Python]

[Azure Storage-Teamblog]: http://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage-SDK für Python]: https://github.com/Azure/azure-storage-python

<!----HONumber=AcomDC_0224_2016-->