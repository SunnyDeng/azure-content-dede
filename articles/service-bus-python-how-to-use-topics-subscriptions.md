<properties 
	pageTitle="Verwenden von Service Bus-Themen (Python) - Azure" 
	description="Erfahren Sie mehr über die Verwendung von Azure Service Bus-Themen und -Abonnements über Python." 
	services="service-bus" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="02/09/2015" 
	ms.author="huvalo"/>





# Verwenden von Servicebus-Themen und -Abonnements
In diesem Leitfaden erfahren Sie, wie Sie Service Bus-Themen und -Abonnements verwenden. Die Beispiele sind in Python geschrieben und verwenden das [Python Azure-Paket][]. Die behandelten Szenarien umfassen das **Erstellen von Themen und Abonnements, Erstellen von Abonnementfiltern, Senden von Nachrichten** an ein Thema, **Empfangen von Nachrichten von einem Abonnement** und
**das Löschen von Themen und Abonnements**. Im Abschnitt [Nächste Schritte](#Next_Steps) finden Sie weitere Informationen zu Themen und Abonnements.

[AZURE.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

**Hinweis:** Informationen zur Installation von Python bzw. des [Python Azure-Paket][] finden Sie im [Python-Installationshandbuch](../python-how-to-install/)


## Erstellen eines Themas

Das **ServiceBusService**-Objekt ermöglicht Ihnen das Arbeiten mit Themen. Fügen Sie am Anfang jeder Python-Datei, in der Sie programmgesteuert auf Azure Service Bus zugreifen möchten, den folgenden Code hinzu:

	from azure.servicebus import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME

Der folgende Code erstellt ein **ServiceBusService**-Objekt. Ersetzen Sie 'mynamespace', 'sharedaccesskeyname' und 'sharedaccesskey' durch den tatsächlichen Namespace, SAS (Shared Access Signature)-Schlüsselnamen und -wert.

	bus_service = ServiceBusService(
		service_namespace='mynamespace',
		shared_access_key_name='sharedaccesskeyname',
		shared_access_key_value='sharedaccesskey')

Den Namen und Wert des SAS-Schlüssels finden Sie in den Verbindungsinformationen des Azure-Portals oder im Eigenschaftsfenster von Visual Studio, wenn Sie den Service Bus-Namespace im Server-Explorer auswählen (wie im vorherigen Abschnitt gezeigt).

	bus_service.create_topic('mytopic')

**create\_topic** unterstützt zudem weitere Optionen, mit denen Sie die Standardthemeneinstellungen überschreiben können, wie zum Beispiel die Nachrichtenlebensdauer oder maximale Themengröße. Das folgende Beispiel zeigt, wie Sie die maximale Themengröße auf 5 GB bei einer Lebensdauer von 1 Minute festlegen:

	topic_options = Topic()
	topic_options.max_size_in_megabytes = '5120'
	topic_options.default_message_time_to_live = 'PT1M'

	bus_service.create_topic('mytopic', topic_options)

## Erstellen von Abonnements

Themenabonnements werden ebenfalls mit dem **ServiceBusService**-Objekt erstellt. Abonnements werden benannt und können einen optionalen Filter aufweisen, der die Nachrichten einschränkt, die an die virtuelle Warteschlange des Abonnements übermittelt werden.

**Hinweis**: Abonnements sind persistent und bleiben erhalten, bis sie selbst oder die mit ihnen verbundenen Themen gelöscht werden.

### Erstellen eines Abonnements mit dem Standardfilter (MatchAll)

**MatchAll** ist der Standardfilter, der verwendet wird, wenn beim Erstellen eines neuen Abonnements kein Filter angegeben wird. Bei Verwendung des Filters **MatchAll** werden alle für das Thema veröffentlichten Nachrichten in die virtuelle Warteschlange des Abonnements gestellt. Mit dem folgenden Beispiel werden ein Abonnement namens 'AllMessages' erstellt und der Standardfilter **MatchAll** verwendet.

	bus_service.create_subscription('mytopic', 'AllMessages')

### Erstellen von Abonnements mit Filtern

Sie können auch Filter einrichten, durch die Sie angeben können, welche an ein Thema gesendeten Nachrichten in einem bestimmten Themenabonnement angezeigt werden sollen.

Der von Abonnements unterstützte flexibelste Filtertyp ist
**SqlFilter**, der eine Teilmenge von SQL92 implementiert. SQL-Filter werden auf die Eigenschaften der Nachrichten angewendet, die für das Thema veröffentlicht werden. Weitere Informationen zu den Ausdrücken, die mit einem SQL-Filter verwendet werden können, finden Sie in der Syntax [SqlFilter.SqlExpression][].

Sie können einem Abonnement mithilfe der **create\_rule**-Methode des **ServiceBusService**-Objekts Filter hinzufügen. Durch diese Methode können Sie neue Filter zu einem vorhandenen Abonnement hinzufügen.

**Hinweis**: Da der Standardfilter automatisch auf alle neuen Abonnements angewendet wird, müssen Sie zuerst den Standardfilter entfernen, oder
von **MatchAll** werden alle weiteren ggf. von Ihnen angegebenen Filter überschrieben. Sie können die Standardregel mithilfe der **delete\_rule**-Methode des
**ServiceBusService**-Objekts aufrufen.

Im folgenden Beispiel wird ein Abonnement namens 'HighMessages' mit einem
**SqlFilter**, der nur Nachrichten auswählt, die über eine benutzerdefinierte
**messagenumber**-Eigenschaft größer als 3 verfügen:

	bus_service.create_subscription('mytopic', 'HighMessages')

	rule = Rule()
	rule.filter_type = 'SqlFilter'
	rule.filter_expression = 'messagenumber > 3'

	bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
	bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)

Ebenso erstellt das folgende Beispiel ein Abonnement namens
"LowMessages" mit einem **SqlFilter**, der nur Nachrichten auswählt, deren **messagenumber**-Eigenschaft kleiner oder gleich 3 ist:

	bus_service.create_subscription('mytopic', 'LowMessages')

	rule = Rule()
	rule.filter_type = 'SqlFilter'
	rule.filter_expression = 'messagenumber <= 3'

	bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
	bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)

Wenn jetzt eine Nachricht an das Thema  'mytopic' gesendet wird, wird diese nun stets an die Empfänger des Abonnements  'AllMessages' zugestellt. Sie wird selektiv an die Empfänger der Abonnements 'HighMessages' und'LowMessages' zugestellt (je nach Inhalt der Nachricht).

## Senden von Nachrichten an ein Thema

Um eine Nachricht an ein Service Bus-Thema zu senden, muss die Anwendung die **send\_topic\_message**-Methode des **ServiceBusService**-Objekts verwenden.

Das folgende Beispiel zeigt, wie Sie fünf Testnachrichten an 'mytopic' senden. Beachten Sie, dass der Eigenschaftenwert **messagenumber** jeder Nachricht gemäß der Iteration der Schleife variiert (auf diese Weise wird bestimmt, welche Abonnements die Nachricht erhalten):

	for i in range(5):
		msg = Message('Msg {0}'.format(i).encode('utf-8'), custom_properties={'messagenumber':i})
		bus_service.send_topic_message('mytopic', msg)

Service Bus-Themen unterstützen eine maximale Nachrichtengröße von 256 MB (der Header, der die standardmäßigen und die benutzerdefinierten Anwendungseigenschaften enthält, darf maximal 64 MB groß sein). Die Anzahl der Nachrichten, die ein Thema enthält, ist nicht einegschränkt. Es gilt jedoch ein Grenzwert für die Gesamtgröße der Nachrichten in einem Thema. Die Themengröße wird bei der Erstellung definiert. Die Obergrenze beträgt 5 GB.

## Empfangen von Nachrichten aus einem Abonnement

Nachrichten werden von einem Abonnement über die
**receive\_subscription\_message**-Methode für das **ServiceBusService**-Objekt empfangen:

	msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
	print(msg.body)

Nachrichten werden sofort nach dem Lesen aus dem Abonnement gelöscht, wenn der Parameter
**peek\_lock** auf **False** festgelegt ist. Sie können die Nachricht lesen (einen Blick darauf werfen) und sperren, ohne sie aus der Warteschlange zu löschen, indem Sie den Parameter
**peek\_lock** auf **True** festlegen.

Das Verhalten für das Lesen und Löschen der Nachricht als Teil des Empfangsvorgangs ist das einfachste Modell. Es wird am besten für Szenarien eingesetzt, bei denen es eine Anwendung tolerieren kann, wenn eine Nachricht bei Auftreten eines Fehlers nicht verarbeitet wird. Um dieses Verfahren zu verstehen, stellen Sie sich ein Szenario vor, in dem der Consumer die Empfangsanforderung ausstellt und dann abstürzt, bevor diese verarbeitet wird. Da Service Bus die Nachricht als verwendet markiert hat, wird diese Nachricht verpasst, die vor dem Absturz verwendet wurde, wenn die Anwendung neu gestartet wird und erneut mit der Verwendung von Nachrichten beginnt.


Wenn der Parameter **peek\_lock** auf **True** festgelegt ist, wird der Empfangsvorgang zu einem zweistufigen Vorgang. Dadurch können Anwendungen unterstützt werden, die fehlende Nachrichten nicht tolerieren können. Wenn Service Bus eine Anfrage erhält, ermittelt der Dienst die nächste zu verarbeitende Nachricht, sperrt diese, um zu verhindern, dass andere Consumer sie erhalten, und sendet sie dann zurück an die Anwendung.
Nachdem die Anwendung die Verarbeitung der Nachricht abgeschlossen hat (oder sie zwecks zukünftiger Verarbeitung zuverlässig gespeichert hat), führt Sie die zweite Phase des Empfangsprozesses durch Aufrufen der **delete**-Methode des **Message**-Objekts aus.
Die **delete**-Methode markiert die Nachricht als verarbeitet, und entfernt sie aus dem Abonnement.

	msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
	print(msg.body)

	msg.delete()


## Behandeln von Anwendungsabstürzen und nicht lesbaren Nachrichten

Service Bus stellt Funktionen zur Verfügung, die Sie bei der ordnungsgemäßen Behandlung von Fehlern in der Anwendung oder bei Problemen beim Verarbeiten einer Nachricht unterstützen. Wenn eine Empfängeranwendung die Nachricht aus einem bestimmten Grund nicht verarbeiten kann, kann sie die **unlock**-Methode für das
**Message**-Objekt aufrufen. Dies führt dazu, dass Service Bus die Nachricht innerhalb des Abonnements entsperrt und zur Verfügung stellt, damit sie erneut empfangen werden kann - durch die gleiche verarbeitende Anwendung oder durch eine andere verarbeitenden Anwendung.

Zudem wird einer im Abonnement gesperrten Nachricht ein Zeitlimit zugeordnet. Wenn die Anwendung die Nachricht vor Ablauf des Sperrzeitlimits nicht verarbeiten kann (zum Beispiel, wenn die Anwendung abstürzt), entsperrt
Service Bus die Nachricht automatisch und macht sie verfügbar, damit sie erneut empfangen werden kann.

Falls die Anwendung nach der Verarbeitung der Nachricht, aber vor Abrufen der **delete**-Methode abstürzt, wird die Nachricht wieder an die Anwendung zugestellt, wenn diese neu gestartet wird. Dies wird häufig als
**At Least Once Processing** (mindestens einmalige Verarbeitung) bezeichnet und bedeutet, dass jede Nachricht mindestens einmal verarbeitet wird, unter bestimmten Umständen jedoch ggf. auch erneut übermittelt wird. Wenn eine doppelte Verarbeitung in dem Szenario nicht zulässig ist, sollten Anwendungsentwickler ihrer Anwendung zusätzliche Logik für den Umgang mit der Zustellung doppelter Nachrichten hinzufügen. Dies erfolgt häufig mit der
**MessageId**-Eigenschaft der Nachricht, die über mehrere Übermittlungsversuche hinweg konstant bleibt.

## Löschen von Themen und Abonnements

Themen und Abonnements sind dauerhaft und müssen explizit über das Azure-Verwaltungsportal oder programmgesteuert gelöscht werden.
Das folgende Beispiel zeigt, wie Sie das Thema namens 'mytopic' löschen:

	bus_service.delete_topic('mytopic')

Durch das Löschen eines Themas werden auch alle Abonnements gelöscht, die mit dem Thema registriert sind. Abonnements können auch unabhängig gelöscht werden. Der folgende Code veranschaulicht, wie ein Abonnement namens
"HighMessages" aus dem Thema 'mytopic' gelöscht wird:

	bus_service.delete_subscription('mytopic', 'HighMessages')

## Nächste Schritte

Nachdem Sie nun mit den Grundlagen der Servicebus-Themen vertraut sind, finden Sie unter den folgenden Links weitere Informationen.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Warteschlangen, Themen und Abonnements][].
-   Referenz für [SqlFilter.SqlExpression][].

[Azure-Verwaltungsportal]: http://manage.windowsazure.com
[Python Azure-Paket]: https://pypi.python.org/pypi/azure  
[Warteschlangen, Themen und Abonnements]: http://msdn.microsoft.com/library/hh367516.aspx
[SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx

<!--HONumber=47-->
