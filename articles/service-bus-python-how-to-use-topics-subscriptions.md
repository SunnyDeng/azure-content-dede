<properties linkid="develop-python-service-bus-topics" urlDisplayName="Service Bus Topics" pageTitle="How to use Service Bus topics (Python) - Azure" metaKeywords="Get started Azure Service Bus topics publising subscribe messaging Python" description="Learn how to use Service Bus topics and subscriptions in Azure. Code samples are written for Python applications." metaCanonical="" services="service-bus" documentationCenter="Python" title="How to Use Service Bus Topics/Subscriptions" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo"></tags>

# Verwenden von Servicebus-Themen und -Abonnements

In diesem Leitfaden erfahren Sie, wie Sie Service Bus-Themen und -Abonnements
über Python-Anwendungen verwenden. Die behandelten Szenarien umfassen das
**Erstellen von Themen und Abonnements, Erstellen von
Abonnementfiltern**, **Senden von Nachrichten an ein Thema, Empfangen von
Nachrichten von einem Abonnement** und das **Löschen von Themen und Abonnements**. Weitere Informationen zu
Themen und Abonnements finden Sie im Abschnitt [Nächste Schritte][Nächste Schritte].

## Inhaltsverzeichnis

-   [Was sind Service Bus-Themen und -Abonnements?][Was sind Service Bus-Themen und -Abonnements?]
-   [Erstellen eines Dienstnamespace][Erstellen eines Dienstnamespace]
-   [Abrufen der Standard-Anmeldeinformationen für den Namespace][Abrufen der Standard-Anmeldeinformationen für den Namespace]
-   [Gewusst wie: Erstellen eines Themas][Gewusst wie: Erstellen eines Themas]
-   [Gewusst wie: Erstellen von Abonnements][Gewusst wie: Erstellen von Abonnements]
-   [Gewusst wie: Senden von Nachrichten an ein Thema][Gewusst wie: Senden von Nachrichten an ein Thema]
-   [Gewusst wie: Empfangen von Nachrichten aus einem Abonnement][Gewusst wie: Empfangen von Nachrichten aus einem Abonnement]
-   [Gewusst wie: Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten][Gewusst wie: Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten]
-   [Gewusst wie: Löschen von Themen und Abonnements][Gewusst wie: Löschen von Themen und Abonnements]
-   [Nächste Schritte][Nächste Schritte]

[WACOM.INCLUDE [howto-service-bus-topics][howto-service-bus-topics]]

**Hinweis:** Wenn Sie Python oder die Clientbibliotheken installieren müssen, informieren Sie sich im [Python-Installationshandbuch][Python-Installationshandbuch].

## <a name="How_to_Create_a_Topic"></a>Erstellen eines Themas

Das **ServiceBusService**-Objekt ermöglicht Ihnen das Arbeiten mit Themen. Fügen Sie am Anfang jeder Python-Datei, in der Sie programmgesteuert auf Azure Service Bus zugreifen möchten, den folgenden Code hinzu:

    from azure.servicebus import *

Der folgende Code erstellt ein **ServiceBusService**-Objekt. Ersetzen Sie "mynamespace", "sharedaccesskeyname" und "sharedaccesskey" durch den echten Namespace, den Namen des SAS-Schlüssels (Shared Access Signature) und den Wert dieses Schlüssels.

    bus_service = ServiceBusService(
        service_namespace='mynamespace',
        shared_access_key_name='sharedaccesskeyname',
        shared_access_key_value='sharedaccesskey')

Den Namen und Wert des SAS-Schlüssels finden Sie in den Verbindungsinformationen des Azure-Portals oder im Eigenschaftsfenster von Visual Studio, wenn Sie den Service Bus-Namespace im Server-Explorer auswählen (wie im vorherigen Abschnitt gezeigt).

    bus_service.create_topic('mytopic')

**create\_topic** unterstützt zudem weitere Optionen,
mit denen Sie die Standardthemeneinstellungen überschreiben
können, wie zum Beispiel die Nachrichtenlebensdauer oder maximale Themengröße. Das folgende Beispiel zeigt, wie Sie die
maximale Themengröße auf 5 GB bei einer Lebensdauer von 1 Minute festlegen:

    topic_options = Topic()
    topic_options.max_size_in_megabytes = '5120'
    topic_options.default_message_time_to_live = 'PT1M'

    bus_service.create_topic('mytopic', topic_options)

## <a name="How_to_Create_Subscriptions"></a> Erstellen von Abonnements

Themenabonnements werden ebenfalls mit dem **ServiceBusService**
-Objekt erstellt. Abonnements werden benannt und können einen optionalen
Filter aufweisen, der die Nachrichten einschränkt, die an die virtuelle
Warteschlange des Abonnements übergeben werden.

**Hinweis**: Abonnements sind persistent und bleiben erhalten, bis
sie selbst oder die mit ihnen verbundenen Themen gelöscht werden.

### Erstellen eines Abonnements mit dem Standardfilter (MatchAll)

**MatchAll** ist der Standardfilter, der verwendet wird, wenn
beim Erstellen eines neuen Abonnements kein Filter angegeben wird. Bei Verwendung des Filters
**MatchAll** werden alle für das Thema veröffentlichten
Nachrichten in die virtuelle Warteschlange des Abonnements gestellt. Im folgenden Beispiel wird
ein Abonnement namens "AllMessages" erstellt und der Standardfilter
**MatchAll** verwendet.

    bus_service.create_subscription('mytopic', 'AllMessages')

### Erstellen von Abonnements mit Filtern

Sie können auch Filter einrichten, durch die Sie angeben können, welche
an ein Thema gesendeten Nachrichten in einem bestimmten Themenabonnement angezeigt werden sollen.

Der von Abonnements unterstützte flexibelste Filtertyp ist
**SqlFilter**, der eine Teilmenge von SQL92 implementiert. SQL-Filter werden
auf die Eigenschaften der Nachrichten angewendet, die für das Thema veröffentlicht werden. Weitere
Informationen zu den Ausdrücken, die mit einem SQL-Filter verwendet
werden können, finden Sie in der Syntax [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

Sie können mithilfe der **create\_rule**
-Methode des **ServiceBusService**
-Objekts Filter zu einem Abonnement hinzufügen. Durch diese Methode können
Sie neue Filter einem vorhandenen Abonnement hinzufügen.

**Hinweis**: Da der Standardfilter automatisch auf alle neuen
Abonnements angewendet wird, müssen Sie zuerst den Standardfilter
entfernen. Ansonsten überschreibt **MatchAll** alle anderen Filter, die Sie angeben. Sie können
die Standardregel mithilfe der Methode **delete\_rule** des
Objekts **ServiceBusService** entfernen.

Mit dem folgenden Beispiel wird ein Abonnement namens "HighMessages" mit einem
**SqlFilter**-Filter erstellt, der nur Nachrichten auswählt, deren benutzerdefinierte
**messagenumber**-Eigenschaft größer ist als 3:

    bus_service.create_subscription('mytopic', 'HighMessages')

    rule = Rule()
    rule.filter_type = 'SqlFilter'
    rule.filter_expression = 'messagenumber > 3'

    bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
    bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)

Ebenso erstellt das folgende Beispiel ein Abonnement namens
"LowMessages" mit einem **SqlFilter**-Filter, der nur Nachrichten auswählt,
deren Eigenschaft **messagenumber** kleiner oder gleich 3 ist:

    bus_service.create_subscription('mytopic', 'LowMessages')

    rule = Rule()
    rule.filter_type = 'SqlFilter'
    rule.filter_expression = 'messagenumber <= 3'

    bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
    bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)

Wenn eine Nachricht an "mytopic" gesendet wird, wird diese nun stets
an die Empfänger des Themenabonnements "AllMessages"
zugestellt. Sie wird selektiv an die Empfänger der Themenabonnements
"HighMessages" und "LowMessages" zugestellt (je nach Inhalt der Nachricht).

## <a name="How_to_Send_Messages_to_a_Topic"></a> Senden von Nachrichten an ein Thema

Um eine Nachricht an ein Service Bus-Thema zu senden, muss die Anwendung die
**send\_topic\_message**-Methode des
**ServiceBusService**-Objekts verwenden.

Das folgende Beispiel zeigt, wie Sie fünf Testnachrichten an "mytopic"
senden. Beachten Sie, dass der Eigenschaftswert **messagenumber**
jeder Nachricht gemäß der Iteration der Schleife variiert
(dadurch wird bestimmt, welche Abonnements die Nachricht erhalten):

    for i in range(5):
        msg = Message('Msg ' + str(i), custom_properties={'messagenumber':i})
        bus_service.send_topic_message('mytopic', msg)

Service Bus-Themen unterstützen eine maximale Nachrichtengröße von
256 MB (der Header, der die standardmäßigen und die
benutzerdefinierten Anwendungseigenschaften enthält, kann eine maximale Größe von 64 MB haben). Es gibt keine Beschränkung für die Anzahl der
Nachrichten, die ein Thema enthält. Es gibt jedoch eine Obergrenze
für die Gesamtgröße der Nachrichten eines Themas. Die Themengröße wird bei der Erstellung definiert.
Die Obergrenze beträgt 5 GB.

## <a name="How_to_Receive_Messages_from_a_Subscription"></a> Empfangen von Nachrichten aus einem Abonnement

Nachrichten werden von einem Abonnement über die
**receive\_subscription\_message**-Methode auf dem
**ServiceBusService**
-Objekt empfangen:

    msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
    print(msg.body)

Wenn der Parameter **peek\_lock** auf **False** festgelegt ist,
werden Nachrichten nach dem Lesen aus dem Abonnement gelöscht. Sie können die Nachricht lesen
(einen Blick darauf werfen) und sperren, ohne sie aus der Warteschlange zu löschen, indem Sie den Parameter
**peek\_lock** auf **True** festlegen.

Das Verhalten für das Lesen und Löschen der Nachricht
als Teil des Empfangsvorgangs ist das einfachste Modell. Es wird am
besten für Szenarien eingesetzt, bei denen es eine Anwendung tolerieren
kann, wenn eine Nachricht bei Auftreten eines Fehlers nicht verarbeitet wird. Um dieses Verfahren zu verstehen, stellen Sie sich
ein Szenario vor, in dem der Consumer die Empfangsanforderung ausstellt und
dann abstürzt, bevor diese verarbeitet wird. Da die Nachricht von Service Bus als verarbeitet markiert wurde,
wird die vor dem Absturz verarbeitete Nachricht nicht berücksichtigt, wenn die
Anwendung neu gestartet und die Verarbeitung von Nachrichten wieder aufgenommen wird.

Wenn der Parameter **peek\_lock** auf **True** festgelegt ist, wird der
Empfangsvorgang zu einem zweistufigen Vorgang. Dadurch können Anwendungen
unterstützt werden, die das Auslassen bzw. Fehlen von Nachrichten nicht zulassen können. Wenn Service Bus eine
Anforderung erhält, ermittelt der Dienst die nächste zu verarbeitende Nachricht,
sperrt diese, um zu verhindern, dass andere Consumer sie erhalten,
und sendet sie dann an die Anwendung zurück. Nachdem die
Anwendung die Verarbeitung der Nachricht abgeschlossen hat
(oder sie zwecks zukünftiger Verarbeitung zuverlässig gespeichert hat), führt sie die zweite
Phase des Empfangsprozesses durch, indem sie die Methode **delete** für das
Objekt **Message** aufruft. Die Methode **delete** markiert die Nachricht als verarbeitet und entfernt sie aus dem Abonnement.

    msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
    print(msg.body)

    msg.delete()

\#\#<a name="How_to_Handle_Application_Crashes_and_Unreadable_Messages"></a> Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten

Service Bus stellt Funktionen zur Verfügung, die Ihnen bei der
ordnungsgemäßen Behebung von Fehlern in der Anwendung oder bei Problemen beim Verarbeiten einer Nachricht helfen. Wenn
eine Empfängeranwendung die Nachricht aus einem bestimmten Grund
nicht verarbeiten kann, so kann sie die Methode **unlock**
für das Objekt **Message** aufrufen. Dies führt dazu, dass Service Bus die
Nachricht innerhalb des Abonnements entsperrt und verfügbar macht,
damit sie erneut empfangen werden kann, und zwar entweder durch dieselbe
verarbeitende Anwendung oder eine andere.

Zudem wird einer im Abonnement gesperrten Nachricht ein Zeitlimit
zugeordnet. Wenn die Anwendung die Nachricht vor Ablauf des
Sperrzeitlimits nicht verarbeiten kann (zum Beispiel wenn die
Anwendung abstürzt), entsperrt Service Bus die Nachricht automatisch und
macht sie verfügbar, um erneut empfangen zu werden.

Falls die Anwendung nach der Verarbeitung der Nachricht, aber vor
Aufrufen der Methode **delete** abstürzt, wird die Nachricht erneut an
die Anwendung zugestellt, wenn diese neu gestartet wird. Dies wird häufig
 als **At Least Once Processing** (Verarbeitung mindestens einmal)
bezeichnet und bedeutet, dass jede Nachricht mindestens
einmal verarbeitet wird, wobei dieselbe Nachricht in bestimmten Situationen möglicherweise erneut zugestellt wird. Wenn eine doppelte Verarbeitung in dem Szenario
nicht zulässig ist, sollten Anwendungsentwickler ihrer Anwendung
zusätzliche Logik für den Umgang mit der Zustellung doppelter Nachrichten hinzufügen. Dies wird häufig durch
Verwendung der Eigenschaft **MessageId** der Nachricht erzielt, die über
mehrere Zustellungsversuche hinweg konstant bleibt.

## <a name="How_to_Delete_Topics_and_Subscriptions"></a>Löschen von Themen und Abonnements

Themen und Abonnements sind persistent und müssen explizit über das
Azure-Verwaltungsportal oder programmgesteuert gelöscht werden.
Das Beispiel unten zeigt, wie Sie das Thema namens "mytopic" löschen:

    bus_service.delete_topic('mytopic')

Durch das Löschen eines Themas werden auch alle Abonnements
gelöscht, die mit dem Thema registriert sind. Abonnements können auch unabhängig gelöscht werden. Der
folgende Code zeigt, wie ein Abonnement namens
"HighMessages" aus dem Thema "mytopic" gelöscht wird:

    bus_service.delete_subscription('mytopic', 'HighMessages')

## <a name="Next_Steps"></a>Nächste Schritte

Nachdem Sie nun mit den Grundlagen der Service Bus-Themen vertraut
sind, finden Sie unter den folgenden Links weitere Informationen.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Warteschlangen, Themen und Abonnements][Warteschlangen, Themen und Abonnements].
-   API-Referenz für [SqlFilter][SqlFilter].

  [Nächste Schritte]: #Next_Steps
  [Was sind Service Bus-Themen und -Abonnements?]: #what-are-service-bus-topics
  [Erstellen eines Dienstnamespace]: #create-a-service-namespace
  [Abrufen der Standard-Anmeldeinformationen für den Namespace]: #obtain-default-credentials
  [Gewusst wie: Erstellen eines Themas]: #How_to_Create_a_Topic
  [Gewusst wie: Erstellen von Abonnements]: #How_to_Create_Subscriptions
  [Gewusst wie: Senden von Nachrichten an ein Thema]: #How_to_Send_Messages_to_a_Topic
  [Gewusst wie: Empfangen von Nachrichten aus einem Abonnement]: #How_to_Receive_Messages_from_a_Subscription
  [Gewusst wie: Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
  [Gewusst wie: Löschen von Themen und Abonnements]: #How_to_Delete_Topics_and_Subscriptions
  [howto-service-bus-topics]: ../includes/howto-service-bus-topics.md
  [Python-Installationshandbuch]: ../python-how-to-install/
  [Warteschlangen, Themen und Abonnements]: http://msdn.microsoft.com/de-de/library/hh367516.aspx
  [SqlFilter]: http://msdn.microsoft.com/de-de/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
