<properties
	pageTitle="Erste Schritte mit Ereignis-Hubs"
	description="Befolgen Sie dieses Lernprogramm für die ersten Schritte bei der Verwendung von Azure Event Hubs mit C# mithilfe von EventProcessorHost."
	services="service-bus"
	documentationCenter=""
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="core"
	ms.tgt_pltfrm="csharp"
	ms.devlang="csharp"
	ms.topic="hero-article"
	ms.date="04/13/2015"
	ms.author="sethm"/>

# Erste Schritte mit Ereignis-Hubs

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## Einführung

Ein Ereignis-Hub ist ein Dienst, mit dem Sie große Mengen von Daten aus verbundenen Geräte und Anwendungen verarbeiten können. Nach dem Sammeln von Daten auf Ereignis-Hubs können die Daten mithilfe eines Speicherclusters gespeichert oder mit einem Echtzeitanalyse-Anbieter transformiert werden. Diese umfangreiche Ereignissammlung und -verarbeitung ist eine wichtige Komponente moderner Anwendungsarchitekturen, einschließlich des Internets der Dinge.

Dieses Lernprogramm zeigt, wie Sie mit dem Azure-Verwaltungsportal einen Ereignis-Hub erstellen. Sie erfahren in diesem Lernprogramm außerdem, wie Nachrichten an einen Ereignis-Hub mithilfe einer Konsolenanwendung in C# aufgenommen werden können und wie Sie diese parallel mit der C#-[Ereignisprozessorhost]-Bibliothek abrufen.

Zum Durchführen dieses Lernprogramms benötigen Sie Folgendes:

+ Microsoft Visual Studio 2013 oder Microsoft Visual Studio Express 2013 für Windows.

+ Ein aktives Azure-Konto. <br/>Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">1 Monat kostenlose Testversion</a>.

## Erstellen eines Ereignis-Hub

1. Melden Sie sich beim [Azure-Verwaltungsportal] an, und klicken Sie dann am unteren Bildschirmrand auf **+NEU**.

2. Klicken Sie auf **App-Dienste**, **Service Bus**, **Ereignis-Hub** und dann auf **Schnellerfassung**.

   	![][1]

3. Geben Sie einen Namen für den Ereignis-Hub ein. Wählen Sie die gewünschte Region aus, und klicken Sie dann auf **Create a new Event Hub**.

   	![][2]

4. Klicken Sie auf den soeben erstellten Namespace (normalerweise ****Name des Ereignis-Hubs*-ns**).

   	![][3]

5. Klicken Sie am oberen Seitenrand auf die Registerkarte **Ereignis-Hubs**, und klicken Sie dann auf den soeben erstellten Ereignis-Hub.

   	![][4]

6. Klicken Sie oben auf die Registerkarte **Konfigurieren**, fügen Sie eine Regel namens **SendRule** mit *Send*-Rechten hinzu, fügen Sie eine weitere Regel mit dem Namen **ReceiveRule** mit den Rechten *Manage, Send und Listen* hinzu, und klicken Sie auf **Speichern**.

   	![][5]

7. Klicken Sie am oberen Seitenrand auf die Registerkarte **Dashboard**, und klicken Sie dann auf Verbindungsinformationen.**Verbindungsinformationen**. Notieren Sie sich die beiden Verbindungszeichenfolgen, oder kopieren Sie sie für die spätere Verwendung in diesem Lernprogramm.

   	![][6]

Ihr Ereignis-Hub wird jetzt erstellt, und Sie verfügen über die zum Senden und Empfangen von Ereignissen erforderlichen Verbindungszeichenfolgen.

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## Ausführen der Anwendungen

Sie können jetzt die Anwendung ausführen.

1.	Führen Sie in Visual Studio das Projekt **Receiver** aus, und warten Sie dann darauf, dass die Empfänger für alle Partitionen gestartet werden.

   	![][21]

2.	Führen Sie das Projekt **Sender** aus, drücken Sie die **EINGABETASTE** im Konsolenfenster, und beobachten Sie, wie die Ereignisse im Empfängerfenster angezeigt werden.

   	![][22]

## Nächste Schritte

Nachdem Sie eine funktionierende Anwendung erstellt haben, die einen Ereignis-Hub erstellt und Daten sendet und empfängt, können Sie mit den folgenden Szenarios fortfahren:

- Eine vollständige [Beispielanwendung mit Verwendung von Ereignis-Hubs].
- Das Beispiel zum [Horizontales Hochskalieren der Ereignisverarbeitung mit Ereignis-Hubs].
- Eine [Messaginglösung mit Warteschlange] unter Verwendung von Service Bus-Warteschlangen.

<!-- Images. -->
[1]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/create-event-hub1.png
[2]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/create-event-hub2.png
[3]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/create-event-hub3.png
[4]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/create-event-hub4.png
[5]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/create-event-hub5.png
[6]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/create-event-hub6.png

[21]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Ereignisprozessorhost]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Übersicht über Ereignis-Hubs]: http://msdn.microsoft.com/library/azure/dn836025.aspx
[Beispielanwendung mit Verwendung von Ereignis-Hubs]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-286fd097
[Horizontales Hochskalieren der Ereignisverarbeitung mit Ereignis-Hubs]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-45f43fc3
[Messaginglösung mit Warteschlange]: ../cloud-services-dotnet-multi-tier-app-using-service-bus-queues.md


<!--HONumber=52--> 