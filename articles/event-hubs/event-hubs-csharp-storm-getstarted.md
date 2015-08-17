<properties
	pageTitle="Erste Schritte mit Event Hubs"
	description="Befolgen Sie dieses Lernprogramm für die ersten Schritte bei der Verwendung von Azure Event Hubs für das Senden von Ereignissen in C# und das Empfangen in einem Apache Storm-Cluster."
	services="event-hubs"
	documentationCenter=""
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="core"
	ms.tgt_pltfrm="csharp"
	ms.devlang="csharp"
	ms.topic="article" 
	ms.date="07/21/2015"
	ms.author="sethm"/>

# Erste Schritte mit Event Hubs

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## Einführung

Event Hubs sind ein hochgradig skalierbares Aufnahmesystem, das Millionen von Ereignissen pro Sekunde aufnehmen kann, wodurch eine Anwendung die Möglichkeit erhält, die von Ihren verbundenen Geräten und Anwendungen erzeugten immensen Datenmengen zu verarbeiten und zu analysieren. Nach der Erfassung in Event Hubs können Sie Daten über einen beliebigen Echtzeitanalyseanbieter oder ein Speichercluster transformieren und speichern.

Weitere Informationen finden Sie unter [Übersicht über Event Hubs].

In diesem Lernprogramm erfahren Sie, wie Nachrichten an einen Ereignis-Hub mithilfe einer Konsolenanwendung in C# aufgenommen werden können und wie Sie diese parallel mit Apache Storm abrufen können.

Zum Durchführen dieses Lernprogramms benötigen Sie Folgendes:

+ Microsoft Visual Studio Express 2013 für Windows

+ Eine Java-Entwicklungsumgebung, die zum Ausführen von [Maven](http://maven.apache.org/) konfiguriert ist. In diesem Lernprogramm wird von [Eclipse](https://www.eclipse.org/) ausgegangen.

+ Ein aktives Azure-Konto. <br/>Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Kostenlose Azure-Testversion</a>.

## Erstellen eines Event Hubs

1. Melden Sie sich beim [Azure-Verwaltungsportal] an, und klicken Sie unten auf dem Bildschirm auf **NEU**.

2. Klicken Sie auf **App Services**, **Service Bus**,** Event Hub** und auf **Schnellerfassung**.

	![][1]

3. Geben Sie einen Namen für den Event Hub ein. Wählen Sie die gewünschte Region aus, und klicken Sie dann auf **Neuen Event Hub erstellen**.

	![][2]

4. Klicken Sie auf den soeben erstellten Namespace (üblicherweise ***Name\_des\_Event\_Hubs*-ns**).

	![][3]

5. Klicken Sie am oberen Seitenrand auf die Registerkarte **Event Hubs**, und klicken Sie dann auf den soeben erstellten Event Hub.

	![][4]

6. Klicken Sie am oberen Seitenrand auf die Registerkarte **Konfigurieren**, und fügen Sie eine Regel namens **SendRule** mit *Send*-Berechtigung hinzu. Fügen Sie eine weitere Regel namens **ReceiveRule** mit der Berechtigung *Listen* hinzu, und klicken Sie dann auf **Speichern**.

	![][5]

7. Beachten Sie auf derselben Seite die generierten Schlüssel für **ReceiveRule**.

	![][6c]

8. Wählen Sie oben die Registerkarte **Dashboard**, und klicken Sie danach auf **Connection Information**. Notieren Sie sich die beiden Verbindungszeichenfolgen.

	![][6]

Ihr Event Hub wird jetzt erstellt, und Sie verfügen über die zum Senden und Empfangen von Ereignissen erforderlichen Verbindungszeichenfolgen.

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## Ausführen der Anwendungen

Sie können jetzt die Anwendung ausführen.

1.	Führen Sie die **LogTopology**-Klasse aus Eclipse aus, und warten Sie dann, bis sie die Empfänger für alle Partitionen gestartet hat.

2.	Führen Sie das Programm **Sender** aus, drücken Sie im Konsolenfenster die **Eingabetaste**, und sehen Sie sich die im Empfängerfenster angezeigten Ereignisse an.

	![][22]

## Nächste Schritte

Nachdem Sie eine funktionierende Anwendung erstellt haben, die einen Ereignis-Hub erstellt und Daten sendet und empfängt, können Sie mit den folgenden Szenarios fortfahren:

- Eine vollständige [Beispielanwendung mit Verwendung von Ereignis-Hubs].
- Das Beispiel [Horizontales Hochskalieren der Ereignisverarbeitung mit Event Hubs]
- Eine [Messaginglösung mit Warteschlange] unter Verwendung von Service Bus-Warteschlangen.

<!-- Images. -->
[1]: ./media/event-hubs-csharp-storm-getstarted/create-event-hub1.png
[2]: ./media/event-hubs-csharp-storm-getstarted/create-event-hub2.png
[3]: ./media/event-hubs-csharp-storm-getstarted/create-event-hub3.png
[4]: ./media/event-hubs-csharp-storm-getstarted/create-event-hub4.png
[5]: ./media/event-hubs-csharp-storm-getstarted/create-event-hub5.png
[6]: ./media/event-hubs-csharp-storm-getstarted/create-event-hub6.png
[6c]: ./media/event-hubs-csharp-storm-getstarted/create-event-hub6c.png

[22]: ./media/event-hubs-csharp-storm-getstarted/receive-storm1.png

<!-- Links -->
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Übersicht über Event Hubs]: event-hubs-overview.md
[Beispielanwendung mit Verwendung von Ereignis-Hubs]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-286fd097
[Horizontales Hochskalieren der Ereignisverarbeitung mit Event Hubs]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-45f43fc3
[Messaginglösung mit Warteschlange]: ../service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 

<!---HONumber=August15_HO6-->