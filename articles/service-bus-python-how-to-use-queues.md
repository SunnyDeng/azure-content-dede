<properties urlDisplayName="Service Bus Queues" pageTitle="Verwenden von Service Bus-Warteschlangen (Python) - Azure"," MetaKeywords "=" Azure Service Bus-Warteschlangen, Azure-Warteschlangen, Azure Messaging, Azure-Warteschlangen Python" description="Learn how to use Service Bus queues in Azure. Code samples written in Python." metaCanonical="" services="service-bus" documentationCenter="Python" title="How to Use Service Bus Queues" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="09/19/2014" ms.author="huvalo" />




# Einsatz von Servicebus-Warteschlangen
In diesem Leitfaden erfahren Sie, wie Sie die Servicebus-Warteschlangen verwenden können. Die Beispiele sind
in Python geschrieben und verwenden das Python Azure-Modul. Die Szenarien
behandeln die Themen **Erstellen von Warteschlangen, Senden und Empfangen von Nachrichten** und
**Löschen der Warteschlangen**. Weitere Informationen zu Warteschlangen finden Sie im Abschnitt [Nächste Schritte][].

## Inhaltsverzeichnis

-   [Was sind Servicebus-Warteschlangen?][]
-   [Erstellen eines Dienstnamespace][]
-   [Abrufen der Standard-Anmeldeinformationen für den Namespace][]
-   [Gewusst wie: Erstellen einer Warteschlange][]
-   [Gewusst wie: Senden von Nachrichten an eine Warteschlange][]
-   [Gewusst wie: Empfangen von Nachrichten aus einer Warteschlange][]
-   [Gewusst wie: Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten][]
-   [Nächste Schritte][]

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

**Hinweis:** Wenn Sie Python oder die Clientbibliotheken installieren müssen, informieren Sie sich im [Python-Installationshandbuch](../python-how-to-install/).


## <a name="create-queue"> </a>Erstellen einer Warteschlange

Das Objekt **ServiceBusService** ermöglicht es Ihnen, mit Warteschlangen zu arbeiten. Fügen Sie am Anfang jeder Python-Datei, in der Sie programmgesteuert auf Azure Service Bus zugreifen möchten, den folgenden Code hinzu:

	from azure.servicebus import ServiceBusService, Message, Queue

Der folgende Code erstellt ein **ServiceBusService**-Objekt. Ersetzen Sie "mynamespace", "sharedaccesskeyname" und "sharedaccesskey" durch den echten Namespace, den Namen des SAS-Schlüssels (Shared Access Signature) und den Wert dieses Schlüssels.

	bus_service = ServiceBusService(
		service_namespace='mynamespace',
		shared_access_key_name='sharedaccesskeyname',
		shared_access_key_value='sharedaccesskey')

Den Namen und Wert des SAS-Schlüssels finden Sie in den Verbindungsinformationen des Azure-Portals oder im Eigenschaftsfenster von Visual Studio, wenn Sie den Service Bus-Namespace im Server-Explorer auswählen (wie im vorherigen Abschnitt gezeigt).

	bus_service.create_queue('taskqueue')

**create_queue** unterstützt zudem weitere Optionen,
mit denen Sie die Standardeinstellungen wie z. B. Nachrichtenlebensdauer
oder maximale Warteschlangengröße überschreiben können. Das folgende Beispiel zeigt, wie Sie die
maximale Warteschlangengröße auf 5 GB bei einer Gültigkeitsdauer von 1 Minute festlegen:

	queue_options = Queue()
	queue_options.max_size_in_megabytes = '5120'
	queue_options.default_message_time_to_live = 'PT1M'

	bus_service.create_queue('taskqueue', queue_options)

## <a name="send-messages"> </a>Senden von Nachrichten an eine Warteschlange

Um eine Nachricht an eine Service Bus-Warteschlange zu senden, ruft Ihre Anwendung die
**send\_queue\_message**-Methode für das **ServiceBusService**-Objekt auf.

Das folgende Beispiel zeigt, wie eine Testnachricht an die Warteschlange
mit dem Namen *taskqueue using* **send\_queue\_message** gesendet wird:

	msg = Message(b'Test Message')
	bus_service.send_queue_message('taskqueue', msg)

Service Bus-Warteschlangen unterstützen eine maximale Nachrichtengröße von 256 KB (der Header,
der die standardmäßigen und die benutzerdefinierten Anwendungseigenschaften enthält, kann eine
maximale Größe von 64 KB haben). Es gibt keine Beschränkung für die Anzahl der Nachrichten
in einer Warteschlange aufgenommen, aber es gibt eine Obergrenze für die Gesamtgröße der Nachrichten,
die in eine Warteschlange aufgenommen werden können. Die Warteschlangengröße wird bei der Erstellung definiert.
Die Obergrenze beträgt 5 GB.

## <a name="receive-messages"> </a>Empfangen von Nachrichten aus einer Warteschlange

Nachrichten werden aus einer Warteschlange unter Verwendung von **receive\_queue\_message**
-Methode für das **ServiceBusService**-Objekt empfangen:

	msg = bus_service.receive_queue_message('taskqueue', peek_lock=False)
	print(msg.body)

Nachrichten werden sofort nach dem Lesen aus der Warteschlange gelöscht, wenn der Parameter
**peek\_lock** auf **False** festgelegt ist. Sie können die
Nachricht lesen (peek) und sperren, ohne sie aus der Warteschlange zu löschen, indem Sie den Parameter
**peek\_lock** auf **True** festlegen.

Das Verhalten von Lesen und Löschen der Nachricht als Teil
des Empfangsvorgangs ist das einfachste Modell. Es wird am besten für Szenarien eingesetzt, bei denen
es eine Anwendung tolerieren kann, wenn eine Nachricht bei Auftreten
eines Fehlers nicht verarbeitet wird. Um dies zu verstehen, betrachten Sie ein Szenario, in dem der
Verbraucher die Empfangsanforderung ausstellt und dann vor der Verarbeitung
abstürzt. Da Service Bus die Nachricht als verarbeitet markiert hat,
fehlt die Nachricht, die vor dem Absturz verarbeitet wurde, wenn die Anwendung
neu gestartet wird und wieder Nachrichten verarbeitet.


Wenn der **peek\_lock**-Parameter auf **True** festgelegt ist, wird der Empfang
zu einem zweistufigen Vorgang, der es ermöglicht, Anwendungen zu unterstützen,
in denen fehlende Nachrichten nicht toleriert werden. Wenn Service Bus eine
Anforderung erhält, wird die nächste zu verarbeitende Nachricht gesucht, gesperrt, um zu verhindern,
dass sie von anderen Empfängern erhalten wird, und dann an die Anwendung zurückgegeben.
Nachdem die Anwendung die Verarbeitung der Nachricht beendet (oder sie
zuverlässig für die zukünftige Verarbeitung gespeichert) hat, wird die zweite Phase des
Empfangsvorgangs durchgeführt, indem die **delete**-Methode für das **Message**
-Objekt aufgerufen wird. Die **delete**-Methode markiert die Nachricht als verarbeitet
und entfernt sie aus der Warteschlange.

	msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
	print(msg.body)

	msg.delete()

## <a name="handle-crashes"> </a>Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten

Service Bus stellt Funktionen zur Verfügung, die Ihnen bei der ordnungsgemäßen Behebung von
Fehlern in der Anwendung oder bei Problemen beim Verarbeiten einer Nachricht helfen. Wenn eine
Empfängeranwendung die Nachricht aus einem bestimmten Grund nicht verarbeiten kann,
kann sie die **unlock**-Methode für das 
**Message**-Objekt aufrufen. Dies bewirkt, dass Service Bus die Nachricht
in der Warteschlange entsperrt und für den erneuten Empfang zur Verfügung stellt,
entweder durch dieselbe verarbeitende Anwendung oder durch eine andere verarbeitende
der Anwendung in der Stagingumgebung befindet.

Zudem wird einer im Abonnement gesperrten Anwendung ein Zeitlimit zugeordnet.
Wenn die Anwendung die Nachricht vor
Ablauf des Sperrzeitlimits nicht verarbeiten kann (zum Beispiel wenn die Anwendung abstürzt), entsperrt Service
Bus die Nachricht automatisch und macht sie verfügbar, um erneut
empfangen zu werden.

Falls die Anwendung nach der Verarbeitung der Nachricht
, aber vor Aufruf der **delete**-Methode abstürzt, wird die Nachricht,
der Anwendung erneut zugestellt, wenn diese neu gestartet wird. Dies wird häufig als
**mindestens einmalige Verarbeitung** bezeichnet. d.h., jede Nachricht wird
mindestens einmal verarbeitet, sie kann in bestimmten Situationen jedoch möglicherweise
erneut zugestellt werden. Wenn das Szenario doppelte Verarbeitung nicht zulässt, müssen
die Anwendungsentwickler ihrer Anwendung zusätzliche Logik hinzufügen,
um doppelte Nachrichtenzustellung verarbeiten zu können. Dies erfolgt häufig mit der
**MessageId**-Eigenschaft der Nachricht, die bei allen
Zustellungsversuchen gleich bleibt.

## <a name="next-steps"> </a>Nächste Schritte

Da Sie nun mit den Grundlagen der Service Bus-Warteschlangen vertraut sind, folgen Sie diesen
Links zu weiteren Informationen.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Warteschlangen, Themen und Abonnements.][]

  [Nächste Schritte]: #next-steps
  [Was sind Servicebus-Warteschlangen?]: #what-are-service-bus-queues
  [Erstellen eines Dienstnamespace]: #create-a-service-namespace
  [Abrufen der Standard-Anmeldeinformationen für den Namespace]: #obtain-default-credentials
  [Gewusst wie: Erstellen einer Warteschlange]: #create-queue
  [Gewusst wie: Senden von Nachrichten an eine Warteschlange]: #send-messages
  [Gewusst wie: Empfangen von Nachrichten aus einer Warteschlange]: #receive-messages
  [Gewusst wie: Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten]: #handle-crashes
  [Warteschlangenkonzepte]: ../../../DevCenter/dotNet/Media/sb-queues-08.png
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  
  
  
  
  
  [Warteschlangen, Themen und Abonnements.]: http://msdn.microsoft.com/de-de/library/windowsazure/hh367516.aspx
