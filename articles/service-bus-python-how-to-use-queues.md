<properties linkid="develop-python-service-bus-queues" urlDisplayName="Service Bus Queues" pageTitle="How to use Service Bus queues (Python) - Azure" metaKeywords="Azure Service Bus queues, Azure queues, Azure messaging, Azure queues Python" description="Learn how to use Service Bus queues in Azure. Code samples written in Python." metaCanonical="" services="service-bus" documentationCenter="Python" title="How to Use Service Bus Queues" authors="" solutions="" manager="" editor="" />

Verwenden von Service Bus-Warteschlangen
========================================

In diesem Leitfaden erfahren Sie, wie Sie die Service Bus-Warteschlangen verwenden können. Die Beispiele sind in Python geschrieben und verwenden das Python-Azure-Modul. Die Szenarien behandeln die Themen **Erstellen von Warteschlangen, Senden und Empfangen von Nachrichten** und **Löschen von Warteschlangen**. Weitere Informationen zu Warteschlangen finden Sie im Abschnitt [Nächste Schritte](#next-steps).

Inhaltsverzeichnis
------------------

-   [Was sind Service Bus-Warteschlangen?](#what-are-service-bus-queues)
-   [Erstellen eines Dienstnamespaces](#create-a-service-namespace)
-   [Abrufen der Standard-Anmeldeinformationen für den Namespace](#obtain-default-credentials)
-   [Gewusst wie: Erstellen einer Warteschlange](#create-queue)
-   [Gewusst wie: Senden von Nachrichten an eine Warteschlange](#send-messages)
-   [Gewusst wie: Empfangen von Nachrichten aus einer Warteschlange](#receive-messages)
-   [Gewusst wie: Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten](#handle-crashes)
-   [Nächste Schritte](#next-steps)

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

**Hinweis:** Informationen zur Installation von Python oder den Clientbibliotheken finden Sie im [Python-Installationshandbuch](../python-how-to-install/).

Erstellen einer Warteschlange
-----------------------------

Das Objekt **ServiceBusService** ermöglicht es Ihnen, mit Warteschlangen zu arbeiten. Fügen Sie am Anfang jeder Python-Datei, in der Sie programmgesteuert auf Azure Service Bus zugreifen möchten, den folgenden Code hinzu:

    from azure.servicebus import *

Der folgende Code erstellt ein **ServiceBusService**-Objekt. Ersetzen Sie 'mynamespace', 'mykey' und 'myissuer' durch die tatsächlichen Werte für Namespace, Schlüssel und Aussteller.

    bus_service = ServiceBusService(service_namespace='mynamespace', account_key='mykey', issuer='myissuer')

    bus_service.create_queue('taskqueue')

**create\_queue** unterstützt zudem weitere Optionen, mit denen Sie Standardeinstellungen für die Warteschlange überschreiben können, wie zum Beispiel die Gültigkeitsdauer von Nachrichten oder die maximale Warteschlangengröße. Das folgende Beispiel zeigt, wie Sie die maximale Warteschlangengröße auf 5 GB bei einer Gültigkeitsdauer von 1 Minute festlegen:

    queue_options = Queue()
    queue_options.max_size_in_megabytes = '5120'
    queue_options.default_message_time_to_live = 'PT1M'

    bus_service.create_queue('taskqueue', queue_options)

Senden von Nachrichten an eine Warteschlange
--------------------------------------------

Um eine Nachricht an eine Service Bus-Warteschlange zu senden, ruft Ihre Anwendung die **send\_queue\_message**-Methode auf dem **ServiceBusService**-Objekt auf.

Das folgende Beispiel zeigt, wie eine Testnachricht an die Warteschlange *taskqueue* mithilfe von **send\_queue\_message** gesendet wird:

    msg = Message('Test Message')
    bus_service.send_queue_message('taskqueue', msg)

Service Bus-Warteschlangen unterstützen eine maximale Nachrichtengröße von 256 KB (der Header, der die standardmäßigen und die benutzerdefinierten Anwendungseigenschaften enthält, kann eine maximale Größe von 64 KB haben). Bei der Anzahl der Nachrichten, die in einer Warteschlange aufgenommen werden können, besteht keine Beschränkung. Allerdings gilt eine Deckelung bei der Gesamtgröße der in einer Warteschlange aufzunehmenden Nachrichten. Die Warteschlangengröße wird bei der Erstellung definiert. Die Obergrenze beträgt 5 GB.

Empfangen von Nachrichten aus einer Warteschlange
-------------------------------------------------

Nachrichten werden von einer Warteschlange über **receive\_queue\_message**-Methode des **ServiceBusService**-Objekts empfangen:

    msg = bus_service.receive_queue_message('taskqueue')
    print(msg.body)

Nachrichten werden aus der Warteschlange gelöscht, wenn sie gelesen wurden. Sie können jedoch Nachrichten lesen (einen Blick darauf werfen) und sperren, ohne sie aus der Warteschlange zu löschen, indem Sie den optionalen Parameter **peek\_lock** auf **True** festlegen.

Das Standardverhalten für das Lesen und Löschen der Nachricht als Teil des Empfangsvorgangs ist das einfachste Modell. Es wird am besten für Szenarien eingesetzt, bei denen es eine Anwendung tolerieren kann, wenn eine Nachricht bei Auftreten eines Fehlers nicht verarbeitet wird. Um dieses Verfahren zu verstehen, stellen Sie sich ein Szenario vor, in dem der Consumer die Empfangsanforderung ausstellt und dann abstürzt, bevor diese verarbeitet wird. Da Service Bus die Nachricht als verwendet markiert hat, wird er jene Nachricht auslassen, die vor dem Absturz verwendet wurde, wenn die Anwendung neu startet und erneut mit der Verwendung von Nachrichten beginnt.

Wenn der Parameter **peek\_lock** auf **True** festgelegt ist, wird der Empfangsvorgang zu einem zweistufigen Vorgang. Dadurch können Anwendungen unterstützt werden, die das Auslassen bzw. Fehlen von Nachrichten nicht zulassen können. Wenn Service Bus eine Anfrage erhält, ermittelt der Dienst die nächste zu verarbeitende Nachricht, sperrt diese, um zu verhindern, dass andere Consumer sie erhalten, und sendet sie dann an die Anwendung zurück. Nachdem die Anwendung die Verarbeitung der Nachricht abgeschlossen hat (oder sie zwecks zukünftiger Verarbeitung zuverlässig gespeichert hat), führt Sie die zweite Phase des Empfangsprozesses durch Aufrufen der **delete**-Methode des **Message**-Objekts aus. Die **delete**-Methode markiert die Nachricht als verarbeitet, und entfernt sie aus der Warteschlange.

    msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
    print(msg.body)

    msg.delete()

Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten
-------------------------------------------------------------

Service Bus stellt Funktionen zur Verfügung, die Ihnen bei der ordnungsgemäßen Behebung von Fehlern in der Anwendung oder bei Problemen beim Verarbeiten einer Nachricht helfen. Wenn eine Empfängeranwendung die Nachricht aus einem bestimmten Grund nicht verarbeiten kann, so kann sie die **unlock**-Methode zum **Message**-Objekt hinzufügen. Dies führt dazu, dass Service Bus die Nachricht innerhalb der Warteschlange entsperrt und verfügbar macht, damit sie erneut empfangen werden kann, und zwar entweder durch dieselbe verarbeitende Anwendung oder durch eine andere verarbeitende Anwendung.

Zudem wird einer in der Warteschlange gesperrten Anwendung ein Zeitlimit zugeordnet. Wenn die Anwendung die Nachricht vor Ablauf des Sperrzeitlimits nicht verarbeiten kann (zum Beispiel wenn die Anwendung abstürzt), entsperrt Service Bus die Nachricht automatisch und macht sie verfügbar, um erneut empfangen zu werden.

Falls die Anwendung nach der Verarbeitung der Nachricht, aber vor Abrufen der **delete**-Methode abstürzt, wird die Nachricht wieder an die Anwendung zugestellt, wenn diese neu gestartet wird. Dies wird häufig als **At Least Once Processing** (Verarbeitung mindestens einmal) bezeichnet und bedeutet, dass jede Nachricht mindestens einmal verarbeitet wird, wobei dieselbe Nachricht in bestimmten Situationen möglicherweise erneut zugestellt wird. Wenn eine doppelte Verarbeitung in dem Szenario nicht zulässig ist, sollten Anwendungsentwickler ihrer Anwendung zusätzliche Logik für den Umgang mit der Zustellung doppelter Nachrichten hinzufügen. Dies wird häufig durch die Verwendung der **MessageId**-Eigenschaft der Nachricht erzielt, die über mehrere Zustellungsversuche hinweg konstant bleibt.

Nächste Schritte
----------------

Nachdem Sie nun mit den Grundlagen der Service Bus-Warteschlangen vertraut sind, finden Sie unter den folgenden Links weitere Informationen.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Warteschlangen, Themen und Abonnements.](http://msdn.microsoft.com/de-de/library/windowsazure/hh367516.aspx)

