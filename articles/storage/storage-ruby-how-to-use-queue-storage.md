<properties 
	pageTitle="Verwenden des Warteschlangenspeichers mit Ruby | Microsoft Azure" 
	description="Erfahren Sie, wie Sie den Azure-Warteschlangendienst zum Erstellen und Löschen von Warteschlangen sowie zum Einfügen, Abrufen und Löschen von Nachrichten verwenden. Die Beispiele wurden in Ruby geschrieben." 
	services="storage" 
	documentationCenter="ruby" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="03/11/2015" 
	ms.author="tomfitz"/>


# Verwenden des Warteschlangenspeichers mit Ruby

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]

## Übersicht

In diesem Leitfaden wird die Durchführung häufiger Szenarios mit dem Microsoft Azure-Warteschlangendienst erläutert. Die Beispiele wurden mit der Ruby Azure-API erstellt. Zu den Szenarien gehören das **Einfügen**, **Einsehen**, **Abrufen** und **Löschen** von Warteschlangennachrichten sowie das **Erstellen und Löschen von Warteschlangen**.

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Erstellen einer Ruby-Anwendung

Erstellen Sie eine Ruby-Anwendung. Anweisungen finden Sie unter [Erstellen einer Ruby-Anwendung in Azure](/develop/ruby/tutorials/web-app-with-linux-vm/).

## Konfigurieren der Anwendung für den Speicherzugriff

Um den Azure-Speicher zu verwenden, müssen Sie das Ruby-Azure-Paket, das eine Reihe von Bibliotheken enthält, die mit den Speicher-REST-Diensten kommunizieren, herunterladen und verwenden.

### Verwenden von RubyGems zum Abrufen des Pakets

1. Verwenden Sie eine Befehlszeilenschnittstelle wie **PowerShell** (Windows), **Terminal** (Mac) oder **Bash** (Unix).

2. Geben Sie "gem install azure" in das Befehlsfenster ein, um das Gem und Abhängigkeiten zu installieren.

### Importieren des Pakets

Fügen Sie mit Ihrem bevorzugten Texteditor Folgendes oben in die Ruby-Datei an der Stelle ein, an der Sie den Speicher verwenden möchten:

	require "azure"

## Einrichten einer Azure-Speicherverbindung

Das Azure-Modul liest die Umgebungsvariablen **AZURE_STORAGE_ACCOUNT** und **AZURE_STORAGE_ACCESS_KEY** nach Informationen aus, die erforderlich sind, um eine Verbindung zum Azure-Speicherkonto herzustellen. Wenn diese Umgebungsvariablen nicht festgelegt werden, müssen Sie die Kontoinformationen vor dem Verwenden von **Azure::QueueService** mit dem folgenden Code angeben:

	Azure.config.storage_account_name = "<your azure storage account>"
	Azure.config.storage_access_key = "<your Azure storage access key>"

So rufen Sie diese Werte ab:

1. Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an.
2. Navigieren Sie zum Speicherkonto, das Sie verwenden möchten.
3. Klicken Sie unten im Navigationsbereich auf **MANAGE KEYS**.
4. Im eingeblendeten Dialog wird der Name des Speicherkontos, der primäre Zugriffsschlüssel und der sekundäre Zugriffsschlüssel angezeigt. Wählen Sie den primären oder sekundären Zugriffsschlüssel aus.

## Erstellen einer Warteschlange

Der folgende Code erstellt ein **Azure::QueueService**-Objekt, das Ihnen das Arbeiten mit Warteschlangen ermöglicht.

	azure_queue_service = Azure::QueueService.new

Mit der **create_queue()**-Methode können Sie eine Warteschlange mit dem angegebenen Namen erstellen.

	begin
	  azure_queue_service.create_queue("test-queue")
	rescue
	  puts $!
	end

## Einfügen einer Nachricht in eine Warteschlange

Mit der **create_message()**-Methode können Sie eine neue Nachricht erstellen und zur Warteschlange hinzufügen.

	azure_queue_service.create_message("test-queue", "test message")

## Einsehen der nächsten Nachricht

Sie können einen Blick auf die Nachricht am Anfang einer Warteschlange werfen, ohne sie aus der Warteschlange zu entfernen, indem Sie die Methode **peek_messages()** aufrufen. Standardmäßig ruft **peek_messages()** nur eine einzige Nachricht ab. Sie können jedoch auch angeben, wie viele Nachrichten Sie abrufen möchten.

	result = azure_queue_service.peek_messages("test-queue",
	  {:number_of_messages => 10})

## Entfernen der nächsten Nachricht aus der Warteschlange

Sie können eine Nachricht in zwei Schritten aus der Warteschlange entfernen.

1. Wenn Sie **list_messages()** aufrufen, wird standardmäßig die nächste Nachricht aus der Warteschlange abgerufen. Sie können jedoch auch angeben, wie viele Nachrichten Sie abrufen möchten. Die von **list_messages()** zurückgegebene Nachricht ist für anderen Code nicht mehr sichtbar, der Nachrichten aus dieser Warteschlange liest. Sie übergeben das Sichtbarkeits-Zeitlimit in Sekunden als Parameter.

2. Um die Nachricht endgültig aus der Warteschlange zu entfernen, müssen Sie außerdem **delete_message()** aufrufen.

Dieser zweistufige Prozess zum Entfernen von Nachrichten stellt sicher, dass eine andere Codeinstanz dieselbe Nachricht erneut abrufen kann, falls die Verarbeitung aufgrund eines Hardware- oder Softwarefehlers fehlschlägt. Ihr Code ruft **delete_message()** direkt nach der Verarbeitung der Nachricht auf.

	messages = azure_queue_service.list_messages("test-queue", 30)
	azure_queue_service.delete_message("test-queue", 
	  messages[0].id, messages[0].pop_receipt)

## Ändern des Inhalts von Nachrichten in der Warteschlange

Sie können den Inhalt einer Nachricht vor Ort in der Warteschlange ändern. Der folgende Code verwendet die **update_message()**-Methode zum Aktualisieren einer Nachricht. Der Code gibt ein Tupel zurück, das den Pop-Beleg der Warteschlangennachricht und einen Datums-/Uhrzeitwert in UTC zurück, der angibt, wann die Nachricht in der Warteschlange sichtbar sein wird.

	message = azure_queue_service.list_messages("test-queue", 30)
	pop_receipt, time_next_visible = azure_queue_service.update_message(
	  "test-queue", message.id, message.pop_receipt, "updated test message", 
	  30)

## Zusätzliche Optionen für das Entfernen von Nachrichten aus der Warteschlange

Es gibt zwei Möglichkeiten, wie Sie das Abrufen von Nachrichten aus der Warteschlange anpassen können.

1. Sie können auch einen Nachrichtenstapel abrufen.

2. Außerdem können Sie das Unsichtbarkeits-Zeitlimit verkürzen oder verlängern, sodass der Code mehr oder weniger Zeit zur vollständigen Verarbeitung jeder Nachricht benötigt.

Das folgende Codebeispiel verwendet **list_messages()**, um 15 Nachrichten mit einem Aufruf abzurufen. Anschließend werden die Nachrichten ausgegeben und gelöscht. Außerdem wird das Unsichtbarkeits-Zeitlimit auf fünf Minuten pro Nachricht festgelegt.

	azure_queue_service.list_messages("test-queue", 300
	  {:number_of_messages => 15}).each do |m|
	  puts m.message_text
	  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
	end

## Abrufen der Warteschlangenlänge

Sie können die Anzahl der Nachrichten in einer Warteschlange schätzen lassen. Die Methode **get_queue_metadata()** ruft eine ungefähre Nachrichtenanzahl und Metadaten zur Warteschlange vom Warteschlangendienst ab.

	message_count, metadata = azure_queue_service.get_queue_metadata(
	  "test-queue")

## Löschen von Warteschlangen

Zum Löschen einer Warteschlange und aller darin enthaltenen Nachrichten rufen Sie die Methode **delete_queue()** für das Warteschlangenobjekt auf.

	azure_queue_service.delete_queue("test-queue")

## Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen des Warteschlangenspeichers vertraut gemacht haben, folgen Sie diesen Links, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.

- Weitere Informationen finden Sie in der MSDN-Referenz: [Azure-Speicher](http://msdn.microsoft.com/library/azure/gg433040.aspx).
- Besuchen Sie den [Blog des Azure-Speicherteams](http://blogs.msdn.com/b/windowsazurestorage/)
- Besuchen Sie das [Azure SDK for Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby)-Repository auf GitHub

Einen Vergleich zwischen dem in diesem Artikel besprochenen Azure-Warteschlangendienst und den unter [Verwenden von Servicebus-Warteschlangen](/develop/ruby/how-to-guides/service-bus-queues/) besprochenen Azure Servicebus-Warteschlangen finden sie unter [Azure-Warteschlangen und Azure Servicevus-Warteschlangen - Vergleich und Gegenüberstellung](http://msdn.microsoft.com/library/azure/hh767287.aspx)
 

<!---HONumber=July15_HO2-->