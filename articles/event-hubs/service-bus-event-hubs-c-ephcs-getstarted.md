<properties
	pageTitle="Erste Schritte mit Event Hubs"
	description="Befolgen Sie dieses Lernprogramm für die ersten Schritte bei der Verwendung von Azure Event Hubs für das Senden von Ereignissen mit C und das Empfangen in C# mithilfe von EventProcessorHost."
	services="event-hubs,service-bus"
	documentationCenter=""
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="core"
	ms.tgt_pltfrm="c"
	ms.devlang="csharp"
	ms.topic="get-started-article"
	ms.date="06/17/2015"
	ms.author="sethm"/>

# Erste Schritte mit Event Hubs

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## Einführung

Event Hubs sind ein hochgradig skalierbares Aufnahmesystem, das Millionen von Ereignissen pro Sekunde aufnehmen kann, wodurch eine Anwendung die Möglichkeit erhält, die von Ihren verbundenen Geräten und Anwendungen erzeugten immensen Datenmengen zu verarbeiten und zu analysieren. Nach der Erfassung in Event Hubs können Sie Daten über einen beliebigen Echtzeit-Analyseanbieter oder ein Speichercluster transformieren und speichern.

Weitere Informationen finden Sie unter [Übersicht über Event Hubs].

In diesem Lernprogramm erfahren Sie, wie Nachrichten an einen Event Hub mithilfe einer Konsolenanwendung in C aufgenommen werden können und wie Sie diese parallel mit der C#-[Ereignisprozessorhost-Bibliothek] abrufen.

Zum Durchführen dieses Lernprogramms benötigen Sie Folgendes:

+ Eine C-Entwicklungsumgebung. In diesem Lernprogramm wird vom GCC-Stack auf einem [virtuellen Linux-Computer mit Azure](../virtual-machines-linux-tutorial.md) mit Ubuntu 14.04 ausgegangen. Anweisungen für andere Umgebungen werden in externen Links bereitgestellt.

+ Microsoft Visual Studio Express 2013 für Windows

+ Ein aktives Azure-Konto. <br/>Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Kostenlose Azure-Testversion</a>.

## Erstellen eines Event Hubs

1. Melden Sie sich beim [Azure-Verwaltungsportal] an, und klicken Sie unten auf dem Bildschirm auf **NEU**.

2. Klicken Sie auf **App-Dienste**, **Service Bus**, **Event Hub** und dann auf **Schnellerfassung**.

   	![][1]

3. Geben Sie einen Namen für den Event Hub ein. Wählen Sie die gewünschte Region aus, und klicken Sie dann auf **Neuen Event Hub erstellen**.

   	![][2]

4. Klicken Sie auf den soeben erstellten Namespace (üblicherweise ***Name_des_Event_Hubs*-ns**).

   	![][3]

5. Klicken Sie am oberen Seitenrand auf die Registerkarte **Event Hubs**, und klicken Sie dann auf den soeben erstellten Event Hub.

   	![][4]

6. Klicken Sie am oberen Seitenrand auf die Registerkarte **Konfigurieren**, und fügen Sie eine Regel namens **SendRule** mit *Send*-Berechtigung hinzu. Fügen Sie eine weitere Regel namens **ReceiveRule** mit den Berechtigungen *Manage, Send, Listen* hinzu, und klicken Sie dann auf **Speichern**.

   	![][5]

7. Beachten Sie auf derselben Seite die generierten Schlüssel für **SendRule**.

   	![][6b]

8. Klicken Sie am oberen Seitenrand auf die Registerkarte **Dashboard**, und klicken Sie dann auf **Verbindungsinformationen**. Notieren Sie sich die beiden Verbindungszeichenfolgen.

   	![][6]

Ihr Event Hub wird jetzt erstellt, und Sie verfügen über die zum Senden und Empfangen von Ereignissen erforderlichen Verbindungszeichenfolgen.

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## Ausführen der Anwendungen

Sie können jetzt die Anwendung ausführen.

1.	Führen Sie das Projekt **Receiver** in Visual Studio aus, und warten Sie dann, bis es die Empfänger für alle Partitionen gestartet hat.

   	![][21]

2.	Führen Sie das Programm **sender** aus, und sehen Sie sich die im Empfängerfenster angezeigten Ereignisse an.

   	![][24]

<!-- Images. -->
[1]: ./media/service-bus-event-hubs-c-ephcs-getstarted/create-event-hub1.png
[2]: ./media/service-bus-event-hubs-c-ephcs-getstarted/create-event-hub2.png
[3]: ./media/service-bus-event-hubs-c-ephcs-getstarted/create-event-hub3.png
[4]: ./media/service-bus-event-hubs-c-ephcs-getstarted/create-event-hub4.png
[5]: ./media/service-bus-event-hubs-c-ephcs-getstarted/create-event-hub5.png
[6]: ./media/service-bus-event-hubs-c-ephcs-getstarted/create-event-hub6.png
[6b]: ./media/service-bus-event-hubs-c-ephcs-getstarted/create-event-hub6b.png


[21]: ./media/service-bus-event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/service-bus-event-hubs-c-ephcs-getstarted/receive-eph-c.png

<!-- Links -->
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Ereignisprozessorhost-Bibliothek]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Übersicht über Event Hubs]: http://msdn.microsoft.com/library/azure/dn836025.aspx
 

<!---HONumber=July15_HO3-->